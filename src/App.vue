<!-- https://pokeapi.co/api/v2/pokemon-species/1/ -->
<template>
  <input type="text" v-model="keyword" placeholder="Search collection..." autofocus>

  <div class="cards">
    <div class="card" v-for="(detail, index) in search" :key="index" @click="clickCard(detail)">
      <div class="card-face">
        <div class="card-label">
          <img :src="detail.sprites.other.home.front_default" height="100">
          <p style="color:black">{{ detail.name }}</p>
        </div>
      </div>
    </div>
  </div>

  <h1 class="total">
    <img src="./assets/img/logo.png" height="100"><br>
    Total collection: {{ pokemonDetail.length }}
  </h1>

  <!-- <div v-for="(detail, index) in search" :key="index">
    <img :src="detail.sprites.other.home.front_default" height="100">
    <p style="color:black">name: {{ detail.name }}</p>
    <p style="color:black">Height: {{ detail.height }}</p>
  </div> -->

  <!-- Modal -->
  <div id="myModal" class="modal" v-if="isModalOpen">
    <div class="modal-content">
      <img :src="selectedPokemon.sprites.other.home.front_default" class="pokeImage">
      <span class="close" @click="isModalOpen = !isModalOpen">&times;</span>
      <h1>{{ selectedPokemon.name }}<a class="typeLabel">{{ selectedPokemon.types[0].type.name }}</a></h1>
      <div class="detailBox">
        <p><strong>Height:</strong> {{ selectedPokemon.height }}</p>
        <p><strong>Weight:</strong> {{ selectedPokemon.weight }}</p>
        <p><strong>Ability:</strong> {{ selectedPokemon.abilities[0].ability.name }}</p>
      </div>
    </div>
  </div>
</template>

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
      console.log(data)
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
  font-family: 'helvetica neue', helvetica, arial, sans-serif;
}

body {
  background: radial-gradient(#333, #111);
  overflow: hidden;
}

input {
  display: block;
  margin: auto;
  width: 80vw;
  padding: 10px;
  margin-top: 20vh;
  font-size: 30px;
  border-radius: 5px;
}

.total {
  color: #fff;
  left: 50%;
  display: flex;
  bottom: 0;
  position: fixed;
  transform: translateX(-50%);
}

.cards {
  bottom: 30vh;
  display: flex;
  height: 150px;
  padding: 0 50px;
  justify-content: center;
  position: fixed;
  left: 0;
  right: 0;
}

.card {
  height: 200px;
  margin: 0 -25px;
  position: relative;
  width: 150px;

  &:after {
    // uncomment background below to see hit areas
    //background: rgba(255, 0, 0, 0.15);
    bottom: 0;
    content: '';
    left: -60px;
    position: absolute;
    right: -60px;
    top: 0px;
    z-index: 10;
  }
}

.card-face {
  border-radius: 15px;
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
  font-size: 20px;
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
  color: #fff;
  display: block;
  position: fixed;
  z-index: 1;
  padding: 10px;
  bottom: 10px;
  left: 0;
  width: 100%;
}

.pokeImage {
  margin-top: 20px;
  background-color: rgba(255, 255, 255, 0.5);
  width: 100%;
  height: auto;
  border-radius: 20px;
}

/* Modal Content */
.modal-content {
  background: rgb(71, 12, 78);
  background: linear-gradient(rgba(71, 12, 78, 1) 0%, rgba(201, 70, 7, 1) 30%, rgba(70, 15, 15, 1) 100%);
  margin: auto;
  height: 80vh;
  padding: 0 20px;
  border: 3px solid rgb(36, 0, 0);
  width: 400px;
  border-radius: 20px;

  h1 {
    margin: 0;
  }
}

.typeLabel {
  background-color: #00ffea;
  border: none;
  color: #000;
  padding: 5px 20px;
  border-radius: 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  margin: 10px;
  font-size: 15px;
}

.detailBox {
  background-color: #fff;
  height: auto;
  border-radius: 20px;
  padding: 20px;
  color: #000;
}

/* The Close Button */
.close {
  color: #ffffff;
  float: right;
  font-size: 55px;
  font-weight: bold;
  line-height: 35px;
}

.close:hover,
.close:focus {
  color: #000;
  text-decoration: none;
  cursor: pointer;
}
</style>
