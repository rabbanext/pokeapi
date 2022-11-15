<template>
  <img alt="Vue logo" src="./assets/img/logo.png" height="100">
  <h1>My Collection</h1>
  <input type="text" v-model="keyword">
  <!-- {{ keyword }} -->
  <div class="cards">
    <div class="card" v-for="(detail, index) in search" :key="index" @click="clickCard(detail)">
      <div class="card-face">
        <div class="card-label">
          <p style="color:black">name: {{ detail.name }}</p>
        </div>
      </div>
    </div>
  </div>
  <!-- <div v-for="(detail, index) in search" :key="index">
    <img :src="detail.sprites.other.home.front_default" height="100">
    <p style="color:black">name: {{ detail.name }}</p>
    <p style="color:black">Height: {{ detail.height }}</p>
  </div> -->
  <!-- Modal -->
  <div id="myModal" class="modal" v-if="isModalOpen">
    <div class="modal-content">
      <span class="close" @click="isModalOpen = !isModalOpen">&times;</span>
      <p>{{ selectedPokemon.name }}</p>
    </div>
  </div>
</template>
<!-- https://pokeapi.co/api/v2/pokemon-species/1/ -->
<script>
export default {
  data: () => ({
    // pokemonList: [],
    pokemonDetail: [],
    keyword: '',
    isModalOpen: false,
    selectedPokemon: []
  }),
  computed: {
    search() {
      if (this.keyword) {
        return this.pokemonDetail.filter((obj) => {
          // console.log(obj.name)
          return obj.name.match(this.keyword)
        });
      }
      return this.pokemonDetail
    }
  },
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
      // console.log(data)
      this.pokemonDetail.push(data)
    }
    this.pokemonList = pokeData.results
  },
  methods: {
    clickCard(value) {
      // console.log('ok')
      this.selectedPokemon = value
      this.isModalOpen = !this.isModalOpen
    }
  }
}
</script>

<style lang="scss">
html,
body {
  height: 100%;
}

body {
  background: radial-gradient(#333, #111);
  overflow: hidden;
}

.cards {
  bottom: 0;
  display: flex;
  height: 150px;
  padding: 0 50px;
  justify-content: center;
  position: fixed;
  left: 0;
  right: 0;
}

.card {
  height: 150px;
  margin: 0 -25px;
  position: relative;
  width: 100px;

  &:after {
    // uncomment background below to see hit areas
    //background: rgba(255, 0, 0, 0.15);
    bottom: 0;
    content: '';
    left: -60px;
    position: absolute;
    right: -60px;
    top: 0px;
    z-index: 10
  }
}

.card-face {
  bottom: 0;
  content: '';
  left: 0;
  pointer-events: none;
  position: absolute;
  right: 0;
  top: 0;
  transition: 800ms cubic-bezier(0.19, 1, 0.22, 1) transform;

  &:after {
    animation: none;
    background: #fff;
    bottom: 0;
    content: '';
    left: 0;
    opacity: 0;
    position: absolute;
    right: 0;
    top: 0;
  }
}

.card-label {
  font-family: 'helvetica neue', helvetica, arial, sans-serif;
  font-size: 24px;
  font-weight: bold;
  letter-spacing: -0.025em;
  padding: 15px 0 0 15px;
}

$total: 20;

@for $i from 0 through ($total - 1) {
  $hue: ($i / $total) * -360;
  $rotationRange: 50;
  $rotation: ($i - ($total - 1) / 2) / ($total - 2) * $rotationRange;
  $offsetRange: 80;
  $offset: abs(($i - ($total - 1) / 2) / ($total - 2) * $offsetRange);

  .card:nth-child(#{$i + 1}) {
    .card-face {
      background: linear-gradient(-135deg, hsla($hue, 100%, 80%, 1),
          hsla($hue, 90%, 45%, 1));
      box-shadow:
        -5px 5px 5px hsla(0, 0%, 0%, 0.15),
        inset 0 0 0 2px hsla($hue, 100%, 80%, 0.75);
      transform: translateY($offset * 1px) rotate($rotation * 1deg);

      .card-label {
        color: hsla($hue, 100%, 43%, 1);
        text-shadow: -0.025em 0.025em 0 hsla($hue, 100%, 75%, 1);
      }
    }

    &:hover {
      .card-face {
        box-shadow:
          0 10px 20px hsla(0, 0%, 0%, 0.4),
          inset 0 0 0 2px hsla($hue, 100%, 80%, 0.75);
        transform: translateY(-100px) rotate(0deg) scale(2);
        transition-duration: 0ms;
        z-index: 5;

        &:after {
          animation: fade 250ms ease-out forwards;
        }
      }

      &:after {
        top: -175px;
      }
    }
  }
}

@keyframes fade {
  0% {
    opacity: 0.9;
    transform: scale(1);
  }

  100% {
    opacity: 0;
    transform: scale(1.15);
  }
}

// Modal
.modal {
  display: block;
  /* Hidden by default */
  position: fixed;
  /* Stay in place */
  z-index: 1;
  /* Sit on top */
  padding-top: 100px;
  /* Location of the box */
  left: 0;
  top: 0;
  width: 100%;
  /* Full width */
  height: 100%;
  /* Full height */
  overflow: auto;
  /* Enable scroll if needed */
  background-color: rgb(0, 0, 0);
  /* Fallback color */
  background-color: rgba(0, 0, 0, 0.4);
  /* Black w/ opacity */
}

/* Modal Content */
.modal-content {
  background-color: #fefefe;
  margin: auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
}

/* The Close Button */
.close {
  color: #aaaaaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: #000;
  text-decoration: none;
  cursor: pointer;
}
</style>
