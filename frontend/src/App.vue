<template>
  <div class="container">
    <div class="team">
      <p>YOUR TEAM!</p>
      <div class="pokemon-box drop-zone" @drop='onDrop($event,1)' @dragover.prevent @dragenter.prevent>
        <div v-for="pokemon in listOne()" :key="pokemon.id" class="drag-el" draggable="true" @dragstart='startDrag($event,pokemon)'>
          <img :src="pokemon.image_link" alt="foto">
        </div>
      </div>
    </div>
    <ul class="drop-zone" @drop='onDrop($event,2)' @dragover.prevent @dragenter.prevent>
       <div  v-for="pokemon in listTwo()" :key="pokemon.id" class="drag-el" draggable="true" @dragstart='startDrag($event,pokemon)'>
         <div class="card">
           <img :src="pokemon.image_link" alt="foto">
           <p>#{{pokemon.id}}  {{pokemon.name}}</p>
         </div>   
       </div>
    </ul>
  </div>
  
  
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  components: {
    
  },
  data: function(){
    return{
      pokemons:[],
    }
  },
  mounted(){
    axios //chiamata alla sola prima generazione di Pokemon (si può impostare un filtro sulla gen)
      .get('https://pokeapi.co/api/v2/generation/1/')
      /* .then(response => (this.pokemons = response.data.results)) */
      .then((response) => {
          this.pokemons = response.data.pokemon_species;

          //ricavo l'id e immagine dei pokemon che ho chiamato 
          this.pokemons.forEach(item=>{
            let currentId = item.url.split('/');
            currentId.pop();
            item.id = currentId.pop();
            item.image_link = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/${item.id}.png`;
            item.list = 2;
          })
          /* this.pokemons.forEach(item => {
            axios.get(item.url)
                 .then((response)=>{
                   let currentCall = response.data.sprites.front_default;
                   console.log(currentCall, this.pokemonsSprite);

                   item.image_link = currentCall;
                   
                 })
          }) */

      });
      Promise.all(this.pokemons);
  },
  methods: {
    listOne () {
      return this.pokemons.filter(item => item.list === 1);
    },
    listTwo () {
      return this.pokemons.filter(item => item.list === 2);
    },
    startDrag: (evt,item) => {
      evt.dataTransfer.dropEffect = 'move';
      evt.dataTransfer.effectAllowed = 'move';
      evt.dataTransfer.setData('pokemonID',item.id);
    },
    onDrop: (evt,list) => {
      const pokemonID = evt.dataTransfer.getData('pokemonID');
      const pokemon = this.pokemons.find(item=>item.id==pokemonID);
      pokemon.list = list;
    }
}
}
</script>

<style>
 *{
   padding:0;
   margin:0;
   box-sizing: border-box;
 }

 .container{
   background: #ffdc57;
 }

 .team{
   display: flex;
   flex-direction: column;
   align-items: center;
 }

 .team .pokemon-box{
   display: flex;
   width: 900px;
   height: 200px; 
   background-color: white;
   border-radius: 12px;
 }

 ul{
   display: flex;
   flex-wrap: wrap;
   list-style-type: none;
   
 }

 ul .drag-el {
   margin: 20px;
 }

 .card{
   max-width: 300px;
   display: flex;
   flex-direction: column;
   align-items: center;

   border-radius: 12px;
   background-color: white;
   padding: 10px;
 }

 .card p {
   text-transform: capitalize;
 }

 .card img, .pokemon-box img{
   width: 150px;
 }

 /* .drop-zone {
    background-color: #eee;
    margin-bottom: 10px;
    padding: 10px;
  }

  .drag-el {
    background-color: #fff;
    margin-bottom: 10px;
    padding: 5px;
  } */
</style>
