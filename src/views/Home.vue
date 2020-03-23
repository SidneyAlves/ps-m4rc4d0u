<template>
<div>
  <div v-if="isLoading">
    <b-spinner class="spinner" label="Carregando"></b-spinner>
  </div>

  <div v-else class="home">
    <div class="row">
      <b-card no-body class="overflow-hidden col-md-4 offset-md-4 col-sm-8 offset-sm-2">
        <b-row no-gutters>
          <b-col md="6" class="center-img">
            <b-card-img :src="require(`@/assets/falcon.png`)" class="rounded-0"></b-card-img>
          </b-col>
          <b-col md="6">
            <b-card-body>
              <hr>
              <b-card-title>{{starship.name}}</b-card-title>
              <hr>
              <b-card-text>
                <p><b>Modelo: </b> {{starship.model}}</p>
                <p><b>Capacidade de passageiros: </b> {{starship.passengers}} pessoas</p>
                <p><b>Capacidade de peso: </b> {{starship.cargo_capacity}}kg</p>
              </b-card-text>
              <b-button href="#" variant="warning" v-on:click="getPeople">Pegar Passageiros</b-button>
            </b-card-body>
          </b-col>
        </b-row>
      </b-card>
    </div>

    <hr>
    <h1>A bordo da nave:</h1>
    <div class="row">
      <div class="col-md-3 col-sm-4 offset-md-3">
        <div>
          <Card :pearson="hanSolo" :imgUrl="require(`../assets/han_solo.png`)" />
        </div>
      </div>

      <div class="col-md-3 col-sm-6">
        <div>
          <b-card
            :img-src="require(`../assets/icon.png`)"
            img-alt="Image"
            img-top
            class="mb-2"
          >
            <hr>
            <b-card-title>
              <b-form-input placeholder="Nome" v-model="user.name"></b-form-input>
            </b-card-title>
            <hr>
            <b-card-text>
              <p><b>Altura: </b> <b-form-input placeholder="Altura (cm)" v-model="user.height"></b-form-input></p>
              <p><b>Peso: </b> <b-form-input placeholder="Peso* (kg)" type="number" v-model="user.mass"></b-form-input></p>
              <p><b>Gênero: </b> <b-form-input placeholder="Gênero" v-model="user.gender"></b-form-input></p>
            </b-card-text>
          </b-card>
        </div>
      </div>
    </div> 
    <b-button href="#"  variant="warning" v-on:click="getMass">Calcular Peso Total</b-button>
    <hr>
    <div class="container">
      <div class="row">
        <div v-for="(pearson, key) in people" class="col-md" v-bind:key="key">
          <Card :pearson="pearson"/>
        </div>
      </div>
    </div> 
    <Result :starship="starship" :hanSolo="hanSolo" :people="people" :user="user" :atualWeight="atualWeight" :starshipWeight="starshipWeight" :massPeople="massPeople" /> 
  </div>
</div>
  
  
</template>

<script>
//Importando componentes utilizados na página
import Card from '@/components/Card.vue'
import Result from '@/components/Result.vue'

export default {
  name: 'Home',
  data(){
    return{
      starship: {},
      hanSolo: {},
      people: [],
      user: {},
      film: {},
      errors: [],
      atualWeight: 0,
      starshipWeight: 0,
      isLoading: true
    }
  },
  components: {
    Card,
    Result
  },

  methods: {
    //Métodos utilizados
    //Fazendo a requisição para o endpoint da Millenium Falcon e salvando os dados.
    getStarship: async function (){
      try {
        const response = await this.$http.get(`https://swapi.co/api/starships/10/`)
        this.starship = response.data
      } catch (e) {
        this.errors.push(e)
        alert('Oops, me parece que temos um problema com a SWAPI, tente novamente em instantes!')
      }
    },

    //Fazendo a requisição para o endpoint do Han Solo e salvando os dados.
    getHanSolo: async function (){
      try {
        const response = await this.$http.get(`https://swapi.co/api/people/14/`)
        this.hanSolo = response.data
        this.isLoading = false
      } catch (e) {
        this.errors.push(e)
      }
    },

    //Fazendo a requisição para o endpoint de algum personagem aleatório e salvando os dados (será utilizado na função getPeople).
    getPearson: async function(url){
      this.people = []
      try {
        const response = await this.$http.get(url)
        this.people.push(response.data)
      } catch (e) {
        this.errors.push(e)
      }
    },

    //Fazendo a requisição para o endpoint do filme Uma Nova Esperança e salvando os dados (útil para pegar a lista de personagens do filme).
    getFilm: async function(){
      try {
        const response = await this.$http.get(`https://swapi.co/api/films/1/`)
        this.film = response.data
      } catch (e) {
        this.errors.push(e)
      }
    },

    //Função que cria um número aleatório entre 0 e o tamanho do array de personagens do filme.
    getRandomNumber: function(){
      return Math.floor(Math.random() * this.film.characters.length)
    },

    //Essa função primeiramente zera a lista de passageiros aleatórios na nave (útil quando clicar em Pegar Passageiros uma segunda vez),
    //Após, é criada uma auxiliar que irá conter todos os personagens já escolhidos (começa com o Han Solo já que ele sempre é o piloto),
    //É feito um laço de repetição while, onde o index só será incrementado caso o personagem escolhido aleatóriamente já não tenha sido escolhido,
    //Caso o personagem escolhido não seja repetido, ele será adicionado na variável auxiliar para futuras comparações,
    //O personagem é finalmente adicionado ao array de pessoas na nave.
    getPeople: async function(){
      this.people = []
      var aux = ['https://swapi.co/api/people/14/']
      var i = 0;
      while(i<5){
        var urlRandom = this.film.characters[this.getRandomNumber()]
        if(aux.indexOf(urlRandom) == -1){
          aux.push(urlRandom)
          this.getPearson(urlRandom)
          i++
        }
      }
    },
    
    //A função abaixo tem o objetivo de calcular todos os pesos
    getMass: function(){
      var self = this
      this.massPeople = 0
      this.atualWeight = 0
      //debugger
      if(!this.user.mass) this.user.mass = 0
      this.starship.cargo_capacity = parseFloat(this.starship.cargo_capacity)
      //Cálculo de porcentagem: mantive o *1 apenas pela facilidade de leitura da fórmula.
      this.starshipWeight = this.starship.cargo_capacity - (this.starship.cargo_capacity/100)*1
      this.people.map(function(pearson){
        //Retirando a vírgula do peso do Jabba para evitar bug na hora da soma
        if(pearson.mass.indexOf(',') != -1) pearson.mass = pearson.mass.replace(',', '')
        //O personagem com o nome de Wilhuff Tarkin tem um peso desconhecido segundo a API
        //O tratamento feito foi transformar o valor de desconhecido para 100 (kg) visto que
        //Após uma procura no google, eu vi que o personagem em questão se parece com um humano magro
        //O peso foi para 100kg pois como não dá pra saber seu peso exatamente utilizando a SWAPI como fonte,
        //É melhor que seja algo mais pesado para se ter uma boa margem de erro, visando a segurança de todos na nave ;)
        if(pearson.mass == "unknown"){
          pearson.mass = 100
        }
        self.massPeople += parseFloat(pearson.mass)
      })
      //Como a Millennium Falcon estava com 99% de capacidade antes de pousar no planeta, imaginei que
      //O peso do Han Solo já estava considerado dentro desses 99%, logo, só foram calculados os pesos
      //Do input, dos 5 personagens aleatórios e os 99% de capacidade máxima da nave. 
      this.atualWeight = this.massPeople + parseFloat(this.user.mass) + this.starshipWeight

      this.$bvModal.show('modal-1')
    }
  },

  //Funções que serão executadas logo quando a página for montada
  mounted() {
    this.getStarship()
    this.getHanSolo()
    this.getFilm()
  }
}
</script>

<style lang="scss" scoped>
  .center-img{
    display: flex; align-items: center
  }

  .spinner{
    width: 6rem; height: 6rem;
  }
</style>