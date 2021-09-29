<template>
  <DistributedCards :distributedDeck="distributedDeck" />
  <img src="./assets/cards/2_of_clubs.svg">
  
</template>

<script>
import DistributedCards from './components/DistributedCards.vue'
//import image from './assets/cards/2_of_clubs.svg'

export default {
  name: 'App',
  components: {
    DistributedCards
  },

  data(){
    return{
      deck:[
        {type: ''},
        {number: ''},
        {src: ''},
      ],

      distributedDeck:[
        {type: ''},
        {number: ''},
        {src: ''},
      ]
      
    }
  },

  methods:{

    generateDeck(){

      this.deck = [];
      let temporaryDeck = [];
      let types = ['hearts', 'spades', 'clubs', 'diamonds'];
      let numbers = [
        '2', '3', '4', '5', '6', '7', '8', '9', '10',
        'jack', 'queen', 'king', 'ace'
      ];
      let indexes = [];
      for (let i = 0; i < 52; i++) {
        indexes.push(i);
      }

      for (let i = 0; i < 4; i++) {
        for (let j = 0; j < 13; j++) {
          temporaryDeck.push({
            type: types[i],
            number: numbers[j],
            src: './assets/cards/' + numbers[j] + '_of_' + types[i] + '.svg'
          })
        }
      }

      let deckSize = 51;
      do{
        let idx = Math.floor(Math.random() * deckSize);
        let removedIndex = indexes.splice(idx, 1);
        this.deck[removedIndex] = temporaryDeck.pop();
        deckSize -= 1;
      }while(deckSize >= 0);

      console.log(this.deck);

      //getting distributed cards
      for(let i = 0; i < 28; i++){
        this.distributedDeck[i] = this.deck.pop();
      }

      console.log(this.distributedDeck);


    },

  /* computed: {
    image () {
      return require('./assets/cards/' + '2_of_clubs' + '.svg');
    }
  } */

  },

  created(){
    this.generateDeck();
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
