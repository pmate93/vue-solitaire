<template>
  <DistributedCards :distributedCards="distributedCards"
  @onDrag="onDrag"
  @onDrop="onDrop" 
  @startDrag="startDrag" 
  @dragEnd="dragEnd"
  @calculateWidth="calculateWidth"

  />
  <!-- <img src="./assets/cards/2_of_clubs.svg"> -->
  <div
  ref="fakeDragImage"
  class="fakeDragImage"
  style="display: none"
  />

  <div
  ref="realDragImage"
  v-show="isDragging"
  style="position: absolute;"
  class="realDragImage"
  > 
    <div v-for="(image, index) in arrayForDragging" :key="image.id" class="dragImage-container">
      <img :src="image.src" :style="{top: index * 12 + 10 + 'px'}">
    </div>
  </div>


</template>

<script>
import DistributedCards from './components/DistributedCards.vue'
//import image from './assets/cards/2_of_clubs.svg'
//import card_back from './assets/card-back.jpg'
import fake_back from './assets/fake-back.jpg'

export default {
  name: 'App',
  components: {
    DistributedCards
  },
  data(){
    return{
      dragImage: '',
      fake_back: fake_back,
      isDragging: false,
      emptyImage: [],
      cardWidth: 0,
      cardHeight: 0,

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
      ],
      arrayForDragging: []
      
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

    calculateWidth(imageSize){
      this.cardWidth = imageSize.width;
      this.cardHeight = imageSize.height;
    },

    whichImageToDrag(src){
      this.dragImage = src;
    },

    removeGhostImage(){
      var img = new Image();
      img.src = this.fake_back;
      this.emptyImage.push(img);

    },

    dragEnd(card, idx, columnIdx){

      if(idx < this.distributedCards[columnIdx].length-1){
        for (let i = idx; i < this.distributedCards[columnIdx].length; i++) {
          this.distributedCards[columnIdx][i].dragging = false;
        }
      }else{
        card.dragging = false;
      }

      this.isDragging = false;
      this.arrayForDragging = [];

    },

    onDrag(event){

      if (!event.pageX && !event.pageY) return
        //const [offsetX, offsetY] = [-this.cardWidth/2, -this.cardHeight/2];
        const [offsetX, offsetY] = [2,2];
        this.$refs.realDragImage.style.left = event.pageX + offsetX + 'px';
        this.$refs.realDragImage.style.top = event.pageY + offsetY + 'px';
        this.isDragging = true;
    },

    startDrag(event, card, columnIdx){
      this.whichImageToDrag(card.src);

      event.dataTransfer.setDragImage(this.emptyImage[0], 0, 0);
      
      
      //document.getElementById('app').append(this.$refs.realDragImage);
      //event.dataTransfer.setDragImage(this.$refs.fakeDragImage, 0, 0);

      event.dataTransfer.dropEffect = 'move';
      event.dataTransfer.effectAllowed = 'move';
      event.dataTransfer.setData('cardID', card.id);
      event.dataTransfer.setData('oldColumnIdx', columnIdx);
      let indexOfClickedCard = this.distributedCards[columnIdx].findIndex(x => x.id === card.id);
      event.dataTransfer.setData('indexOfClickedCard', indexOfClickedCard);
      
      if(indexOfClickedCard < this.distributedCards[columnIdx].length-1){

        for (let i = indexOfClickedCard; i < this.distributedCards[columnIdx].length; i++) {
          this.arrayForDragging.push(this.distributedCards[columnIdx][i]); 
          setTimeout(()=>{
            this.distributedCards[columnIdx][i].dragging = true;
          },0)
        }
      }else{
        this.arrayForDragging.push(this.distributedCards[columnIdx][this.distributedCards[columnIdx].length-1]);
        setTimeout(()=>{
          this.distributedCards[columnIdx][indexOfClickedCard].dragging = true;
        },0)
      }
    
    },

    onDrop(event, columnIdx){
      this.isDragging = false;
      this.arrayForDragging = [];
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

          //this.arrayForDragging.push(this.distributedCards[oldColumnIdx][i]);

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
    },

    yolo(){
      console.log('object')
    }

  
  },
  created(){
    this.generateDeck();
    this.removeGhostImage();
    //window.addEventListener("resize", this.calculateWidth);
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

img{
  width: 12vh;
  position: absolute;
  
}

.dragImage-container{
  position:relative;
}
</style>
