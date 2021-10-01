<template>
  <DistributedCards :distributedCards="distributedCards" @onDrop="onDrop" @startDrag="startDrag" @dragEnd="dragEnd" />
  <!-- <img src="./assets/cards/2_of_clubs.svg"> -->
  
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
        {id: 0},
        {flipped: false},
        {dragging: false}
      ],

      distributedCards: [
        { columns: [] }
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
            src: require('./assets/cards/' + numbers[j] + '_of_' + types[i] + '.svg'),
            flipped: false
          })
        }
      }

      let deckSize = 51;
      do{
        let idx = Math.floor(Math.random() * deckSize);
        let removedIndex = indexes.splice(idx, 1);
        this.deck[removedIndex] = temporaryDeck.pop();
        this.deck[removedIndex].id = deckSize;
        deckSize -= 1;
      }while(deckSize >= 0);

      console.log(this.deck);

      //getting distributed cards
      let tempDeck = [];

      for(let i = 0; i < 28; i++){
        tempDeck[i] = this.deck.pop();
      }
      
      this.distributedCards = [];
      for(let i = 1; i < 8; i++){
        this.distributedCards[i] = [];
        for (let j = 0; j < i; j++) {
          this.distributedCards[i].push(tempDeck.pop());
        }
        this.distributedCards[i][this.distributedCards[i].length-1].flipped = true;
      }

      console.log(this.distributedCards);

    },

    dragEnd(card){
      card.dragging = false;
    },

    startDrag(event, card, columnIdx){

      event.dataTransfer.dropEffect = 'move';
      event.dataTransfer.effectAllowed = 'move';
      event.dataTransfer.setData('cardID', card.id);
      event.dataTransfer.setData('oldColumnIdx', columnIdx);
      let indexOfClickedCard = this.distributedCards[columnIdx].findIndex(x => x.id === card.id);
      event.dataTransfer.setData('indexOfClickedCard', indexOfClickedCard);
      
      /* let img = document.createElement("img");
      img.src = this.distributedCards[columnIdx][indexOfClickedCard].src;
      img.style.opacity = '100%';
      event.dataTransfer.setDragImage(img, 0, 0); */
      setTimeout(()=>{
        this.distributedCards[columnIdx][indexOfClickedCard].dragging = true;
      },0)
    
    },

    onDrop(event, columnIdx){
      const cardID = event.dataTransfer.getData('cardID');
      const oldColumnIdx = event.dataTransfer.getData('oldColumnIdx');
      const indexOfClickedCard = event.dataTransfer.getData('indexOfClickedCard');
      console.log(cardID, oldColumnIdx, columnIdx);

      /* setTimeout(()=>{
        this.distributedCards[columnIdx][this.distributedCards[columnIdx].length-1].dragging = false;
      },0) */

      if(indexOfClickedCard < this.distributedCards[oldColumnIdx].length-1){
        console.log('yolo');

        let tempArray = [];
        let counter = 0;
        for (let i = indexOfClickedCard; i < this.distributedCards[oldColumnIdx].length; i++) {
          tempArray.push(this.distributedCards[oldColumnIdx][i]);
          this.distributedCards[oldColumnIdx][i].dragging = false;
          counter++;
        }
        console.log(tempArray);

        //adding and removing selected card
        for (let i = 0; i < counter; i++) {
          this.distributedCards[oldColumnIdx].pop();
        }
        this.distributedCards[columnIdx].push(...tempArray);
      }else{
        setTimeout(()=>{
          this.distributedCards[columnIdx][this.distributedCards[columnIdx].length-1].dragging = false;
        },0)
        this.distributedCards[columnIdx].push(this.distributedCards[oldColumnIdx].pop());
      }


      //flipping top card where card was removed
      if(this.distributedCards[oldColumnIdx].length-1 >= 0){
        this.distributedCards[oldColumnIdx][this.distributedCards[oldColumnIdx].length-1].flipped = true;
      }
    }

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
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

#app {
  /* font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px; */

  display: flex;

  
  
}
</style>
