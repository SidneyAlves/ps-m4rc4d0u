<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Aplicação</router-link> |
      <router-link to="/about">Sobre o desafio</router-link>
    </div>
    <router-view/>
  </div>
</template>

<style lang="scss">
html{
  background-color: #0e0e0e;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #cccc00;
  background-color: #0e0e0e;

  .card{
    background-color:#2c3e50;
  }

  hr{
    background-color: #cccc00
  }
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #cccc00;

    &.router-link-exact-active {
      color: #969600;
    }
  }
}
</style>
