<template>
  <b-card
    :img-src="imgUrl"
    img-alt="Image"
    img-top
    class="mb-2"
  >
    <hr>
    <b-card-title>{{pearson.name}}</b-card-title>
    <hr>
    <b-card-text>
      <p> <b>Altura: </b> {{pearson.height}}cm</p>
      <div v-if="pearson.mass == 'unknown'"><p> <b>Peso: </b> 100kg</p></div>
      <p v-else> <b>Peso: </b> {{pearson.mass}}kg</p>
      <p v-if="pearson.gender == 'male'"><b>Gênero: </b> Masculino</p>
      <p v-if="pearson.gender == 'female'"><b>Gênero: </b> Feminino</p>
      <p v-if="pearson.gender == 'hermaphrodite'"><b>Gênero: </b> Hermafrodita</p>
      <p v-if="pearson.gender == 'n/a'"><b>Gênero: </b> N/A</p>  
    </b-card-text>
  </b-card>
</template>

<script>
export default {
  name: 'Card',
  props: {
    pearson: {},
    imgUrl: {
        default: require(`../assets/icon.png`)
    }
  }
}
</script>
