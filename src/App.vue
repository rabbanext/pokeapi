<template>
  <img alt="Vue logo" src="./assets/img/logo.png" height="100">
  <h1>My Collection</h1>
  <ul>
    <!-- <li v-for="(pokemon, index) in pokemonList" :key="`poke-${index}`">
      <a :href="pokemon.url">{{ pokemon.name }}</a>
    </li> -->
    <li v-for="(detail, index) in pokemonDetail" :key="index">
      <img :src="detail.sprites.other.home.front_default">
      <p style="color:black">name: {{ detail.name }}</p>
      <p style="color:black">Height: {{ detail.height }}</p>
    </li>
  </ul>
</template>
<!-- https://pokeapi.co/api/v2/pokemon-species/1/ -->
<script>
export default {
  data: () => ({
    pokemonList: [],
    pokemonDetail: []
  }),
  async mounted() {
    const pokeData = await fetch('https://pokeapi.co/api/v2/pokemon').then(
      response => response.json()
    )
    // console.log(pokeData.results)

    for (let index = 0; index < pokeData.results.length; index++) {
      const element = pokeData.results[index];
      // console.log(element)
      const data = await fetch(element.url).then(
        response => response.json())
      // ini
      console.log(data)
      this.pokemonDetail.push(data)
    }
    this.pokemonList = pokeData.results
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #eee;
  margin-top: 60px;
}
</style>
