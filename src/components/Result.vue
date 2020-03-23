<template>
    <div>
        <b-modal id="modal-1" title="Resultado">
            <table class="table table-striped">
                <thead>
                    <tr>
                    <th scope="col">Nome</th>
                    <th scope="col">Altura</th>
                    <th scope="col">Gênero</th>
                    <th scope="col">Peso</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <th>{{hanSolo.name}} + Carga da Nave</th>
                        <td>{{hanSolo.height}}cm</td>
                        <td>{{hanSolo.gender}}</td>
                        <td>{{starshipWeight}}kg</td>
                    </tr>
                    <tr>
                        <th>{{user.name}}</th>
                        <td>{{user.height}}cm</td>
                        <td>{{user.gender}}</td>
                        <td>{{user.mass}}kg</td>
                    </tr>
                    <tr v-for="(pearson, key) in people" v-bind:key="key">
                        <th>{{pearson.name}}</th>
                        <td>{{pearson.height}}cm</td>
                        <td v-if="pearson.gender == 'male'">Masculino</td>
                        <td v-if="pearson.gender == 'female'">Feminino</td>
                        <td v-if="pearson.gender == 'hermaphrodite'">Hermafrodita</td>
                        <td v-if="pearson.gender == 'n/a'">N/A</td>
                        <div v-if="pearson.mass == 'unknown'"><td>100kg</td></div>
                        <td v-else>{{pearson.mass}}kg</td>
                    </tr>
                </tbody>
            </table>
            <hr>
            <p v-if="starship.cargo_capacity - atualWeight >= 0">Peso livre: {{starship.cargo_capacity - atualWeight}}</p>
            <p v-if="starship.cargo_capacity - atualWeight < 0">Peso Excedente: {{(starship.cargo_capacity - atualWeight)*(-1)}}</p>
            
            <hr>
            <b-alert variant="success" show v-if="starship.cargo_capacity - atualWeight >= 0">A nave consegue comportar o peso atual!</b-alert>
            <b-alert variant="danger" show v-else>A nave não consegue comportar o peso atual!</b-alert>
        </b-modal>
    </div>
</template>

<script>
export default {
  name: 'Result',
  props: {
    starship: {},
    hanSolo: {},
    people: [],
    user: {},
    atualWeight: 0,
    starshipWeight: 0
  }
}
</script>