<template>

  <div class="top">
    <DeckContainer
    :deck="deck"
    :cardsFlippedFromDeck="cardsFlippedFromDeck"
    :isDeckEmpty="isDeckEmpty"
    :topThreeCards="topThreeCards"
    @flipThreeCards="flipThreeCards"
    @cardsBackToDeck="cardsBackToDeck"
    @startDrag="startDrag" 
    @dragEnd="dragEnd"
    @onDrag="onDrag"
    />

    <FinalContainer
    :finalCards="finalCards"
    :cardWidth="cardWidth"
    :cardHeight="cardHeight"
    @onDrop="onDrop"
    @startDrag="startDrag" 
    @dragEnd="dragEnd"
    @onDrag="onDrag"
     />
  </div>

  <DistributedCards :distributedCards="distributedCards"
  @onDrag="onDrag"
  @onDrop="onDrop" 
  @startDrag="startDrag" 
  @dragEnd="dragEnd"
  @calculateWidth="calculateWidth"

  />
  

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
import DistributedCards from './components/DistributedCards.vue';
import DeckContainer from './components/DeckContainer.vue';
import FinalContainer from './components/FinalContainer.vue';
//import image from './assets/cards/2_of_clubs.svg'
//import card_back from './assets/card-back.jpg'
import fake_back from './assets/fake-back.jpg'

export default {
  name: 'App',
  components: {
    DistributedCards,
    DeckContainer,
    FinalContainer
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
        {number: 0},
        {title: ''},
        {color: ''},
        {src: ''},
        {id: 0},
        {flipped: false},
        {dragging: false}
      ],
      distributedCards: [
        { columns: [] }
      ],
      arrayForDragging: [],
      cardsFlippedFromDeck: [],
      topThreeCards: [],
      isDeckEmpty: false,
      finalCards: [
        [0],
        [0],
        [0],
        [0]
      ]
      
    }
  },

  methods:{
    generateDeck(){
      this.deck = [];
      let temporaryDeck = [];
      let types = ['hearts', 'spades', 'diamonds', 'clubs'];
      let titles = [
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
            number: j+2,
            color: i % 2 ? 'black' : 'red',
            title: titles[j],
            src: require('./assets/cards/' + titles[j] + '_of_' + types[i] + '.svg'),
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
    },

    cardsBackToDeck(){
      this.cardsFlippedFromDeck.forEach(card => {
        card.flipped = false;
      });
      this.deck = [...this.cardsFlippedFromDeck];
      this.deck.reverse();
      this.cardsFlippedFromDeck = [];
      this.isDeckEmpty = false;
      this.topThreeCards = [];
      
    },

    flipThreeCards(){
      console.log(this.deck)
      if(this.deck.length !== 0){
        
        if(this.deck.length > 3){
          for(let i = 0; i < 3; i++){
            this.cardsFlippedFromDeck.push(this.deck.pop());  
            this.cardsFlippedFromDeck[this.cardsFlippedFromDeck.length-1].flipped = true;
          }
        }else{
          for(let i = 0; i < this.deck.length; i++){
            this.cardsFlippedFromDeck.push(this.deck.pop());  
            this.cardsFlippedFromDeck[this.cardsFlippedFromDeck.length-1].flipped = true;
          }
        }

        if(this.deck.length === 0){
          setTimeout(()  =>{
            this.isDeckEmpty = true;
          }, 0);
        }

        this.topThreeCards = this.cardsFlippedFromDeck.slice(this.cardsFlippedFromDeck.length - 3, this.cardsFlippedFromDeck.length);

      }else{
        this.deck = [...this.cardsFlippedFromDeck];
        this.cardsFlippedFromDeck = [];
      }
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

    dragEnd(card, idx, columnIdx, fromWhere){
      
      if(columnIdx == -1 || fromWhere === 'fromFinal'){
        card.dragging = false;

      }else{
        
        if(idx < this.distributedCards[columnIdx].length-1){
          for (let i = idx; i < this.distributedCards[columnIdx].length; i++) {
            this.distributedCards[columnIdx][i].dragging = false;
          }
        }else{
          card.dragging = false;
        }
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

    startDrag(event, card, columnIdx, fromWhere){
      this.whichImageToDrag(card.src);
      event.dataTransfer.setDragImage(this.emptyImage[0], 0, 0);
      
      event.dataTransfer.dropEffect = 'move';
      event.dataTransfer.effectAllowed = 'move';
      event.dataTransfer.setData('cardID', card.id);
      event.dataTransfer.setData('oldColumnIdx', columnIdx);
      event.dataTransfer.setData('fromWhere', fromWhere);
      //from final cards
      if(fromWhere === 'fromFinal'){
        this.arrayForDragging.push(this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1]);
        setTimeout(()=>{
          this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].dragging = true;
        },0)
      }
      //from deckContainer
      else if(columnIdx == -1){
        this.arrayForDragging.push(this.cardsFlippedFromDeck[this.cardsFlippedFromDeck.length - 1]);
        setTimeout(()=>{
          this.cardsFlippedFromDeck[this.cardsFlippedFromDeck.length - 1].dragging = true;
        },0)
      }else{
        //from disributed cards
        
        let indexOfClickedCard = this.distributedCards[columnIdx].findIndex(x => x.id === card.id);
        event.dataTransfer.setData('indexOfClickedCard', indexOfClickedCard);
        
        if(indexOfClickedCard < this.distributedCards[columnIdx].length - 1){
  
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
      }


    
    },

    onDrop(event, columnIdx, toWhere){
      this.isDragging = false;
      this.arrayForDragging = [];
      const cardID = event.dataTransfer.getData('cardID');
      const oldColumnIdx = event.dataTransfer.getData('oldColumnIdx'); // if -1 -> from deck
      const indexOfClickedCard = oldColumnIdx !== -1 ? event.dataTransfer.getData('indexOfClickedCard') : null;
      const fromWhere = event.dataTransfer.getData('fromWhere');
      let isEnabledToDrop = false;
      console.log(cardID, oldColumnIdx, columnIdx);
      
      let actCard = this.topThreeCards[this.topThreeCards.length - 1];
      //console.log(this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].type.title)
      //from deck to final cards
      if(toWhere === 'toFinal'){
        
        if(oldColumnIdx === "-1"){
          if(actCard.title === 'ace' && this.finalCards[columnIdx].length === 1){
 
            this.finalCards[columnIdx].push(this.cardsFlippedFromDeck.pop());
            this.topThreeCards.pop();

          }else if(((actCard.type ===  this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].type
           && actCard.number === this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].number + 1) && (this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].title !== 'king'))
           || ((actCard.title === '2') && (this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].title === 'ace') && (actCard.type === this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].type))){
            
            this.finalCards[columnIdx].push(this.cardsFlippedFromDeck.pop());
            this.topThreeCards.pop();
          }

        }else if(fromWhere === 'fromFinal' && toWhere !== 'toDist'){
          //from final to final ace
          if(this.finalCards[oldColumnIdx][this.finalCards[oldColumnIdx].length - 1].title === 'ace' && this.finalCards[columnIdx].length === 1){
            this.finalCards[columnIdx].push(this.finalCards[oldColumnIdx].pop());
          }
        //from dist to final cards
        }else{

          if(parseInt(indexOfClickedCard) === this.distributedCards[oldColumnIdx].length -1){
            actCard = this.distributedCards[oldColumnIdx][this.distributedCards[oldColumnIdx].length - 1];
            if(this.distributedCards[oldColumnIdx][indexOfClickedCard].title === 'ace' && this.finalCards[columnIdx].length === 1){
              
              this.finalCards[columnIdx].push(this.distributedCards[oldColumnIdx].pop());
              //flipping top card where was removed
              if(this.distributedCards[oldColumnIdx].length-1 >= 0){
                this.distributedCards[oldColumnIdx][this.distributedCards[oldColumnIdx].length-1].flipped = true;
              }
            }

            else if(((actCard.type ===  this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].type
             && actCard.number === this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].number + 1) && (this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].title !== 'king'))
             || ((actCard.title === '2') && (this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].title === 'ace') && (actCard.type === this.finalCards[columnIdx][this.finalCards[columnIdx].length - 1].type))){
            
              this.finalCards[columnIdx].push(this.distributedCards[oldColumnIdx].pop());
              //flipping top card where was removed
              if(this.distributedCards[oldColumnIdx].length-1 >= 0){
                this.distributedCards[oldColumnIdx][this.distributedCards[oldColumnIdx].length-1].flipped = true;
              }
            }
          }
        }
      //from final to dist
      }else if(toWhere === 'toDist' && fromWhere === 'fromFinal'){
        actCard = this.finalCards[oldColumnIdx][this.finalCards[oldColumnIdx].length - 1];

        if(this.distributedCards[columnIdx].length === 0){
          if(actCard.title === 'king'){
            setTimeout(()=>{
              actCard.dragging = false;
            },0)
            this.distributedCards[columnIdx].push(this.finalCards[oldColumnIdx].pop());
          }

        }else if(actCard.color !== this.distributedCards[columnIdx][this.distributedCards[columnIdx].length-1].color && actCard.number + 1 === this.distributedCards[columnIdx][this.distributedCards[columnIdx].length-1].number){
            this.distributedCards[columnIdx].push(this.finalCards[oldColumnIdx].pop());
          }

      }else{
        // from deck to dist cards
        if(oldColumnIdx === '-1'){
          
          if(this.distributedCards[columnIdx].length === 0){
            if(actCard.title === 'king'){
              setTimeout(()=>{
                actCard.dragging = false;
              },0)
              this.distributedCards[columnIdx].push(this.cardsFlippedFromDeck.pop());
              this.topThreeCards.pop();
            }
            
          }else if(actCard.color !== this.distributedCards[columnIdx][this.distributedCards[columnIdx].length-1].color && actCard.number + 1 === this.distributedCards[columnIdx][this.distributedCards[columnIdx].length-1].number){
            this.distributedCards[columnIdx].push(this.cardsFlippedFromDeck.pop());
            this.topThreeCards.pop();
          }
        }
        //from dist cards to dist cards
        else if(this.distributedCards[oldColumnIdx][indexOfClickedCard].title === 'king' && this.distributedCards[columnIdx].length === 0){
          isEnabledToDrop = true;
        }
  
        else if(this.distributedCards[columnIdx].length > 0){
          if((this.distributedCards[oldColumnIdx][indexOfClickedCard].color !== this.distributedCards[columnIdx][this.distributedCards[columnIdx].length-1].color) 
        && (this.distributedCards[oldColumnIdx][indexOfClickedCard].number + 1 === this.distributedCards[columnIdx][this.distributedCards[columnIdx].length-1].number)){
            isEnabledToDrop = true;
          }
        }
  
        if(isEnabledToDrop){
          if(indexOfClickedCard < this.distributedCards[oldColumnIdx].length-1){
  
            let tempArray = [];
            let counter = 0;
            for (let i = indexOfClickedCard; i < this.distributedCards[oldColumnIdx].length; i++) {
              tempArray.push(this.distributedCards[oldColumnIdx][i]);
    
              
    
              this.distributedCards[oldColumnIdx][i].dragging = false;
              counter++;
            }
            
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
          //flipping top card where was removed
          if(this.distributedCards[oldColumnIdx].length-1 >= 0){
            this.distributedCards[oldColumnIdx][this.distributedCards[oldColumnIdx].length-1].flipped = true;
          }
        }
      }
    },

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
  position:absolute;
}

img{
  width: 12vh;
  position: absolute;
  
}

.dragImage-container{
  position:relative;
}

.top{
  display: flex;
}
</style>
