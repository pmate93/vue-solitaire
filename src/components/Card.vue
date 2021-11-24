<template>

  <img v-if="flipped"
  @click.right.prevent 
  v-bind:src="this.url"
  ref="image"
  :style="{display: this.dragging ? 'none' : ''}"
  @load="getWidth"
  class="child">

  <img v-else @copy.prevent v-bind:src="card_back" class="child">

</template>

<script>
import card_back from '../assets/card-back.jpg'
export default {
  name: 'Card',
  emits: ['calculateWidth'],
  props:{
    url: String,
    length: Number,
    flipped: Boolean,
    dragging: Boolean,
    isDeckEmpty: Boolean,

  },

  data(){
    return{
      card_back: card_back,
      
      imageSize: {
        width: 0,
        height: 0
      },
    }
  },

  methods: {
    getWidth(){

      if(!this.isDeckEmpty){

        this.imageSize.height = this.$refs.image.clientHeight;
        this.imageSize.width = this.$refs.image.clientWidth;
  
        this.$emit('calculateWidth', this.imageSize);
      }
    },

    setWidthAndHeight(){
      this.imageSize.height = document.getElementsByClassName('child')[0].clientHeight;
      this.imageSize.width = document.getElementsByClassName('child')[0].clientWidth;

      this.$emit('calculateWidth', this.imageSize);
    } 
  },

  created() {
    window.addEventListener("resize", this.setWidthAndHeight)
  }, 

  

}
</script>

<style>
.child{
  position:absolute;
  width: 7.5rem;
  opacity:100%;
  -webkit-touch-callout: none;
  -webkit-user-select: none;
   -khtml-user-select: none;
     -moz-user-select: none;
      -ms-user-select: none;
          user-select: none;
}

img.dragging {
  background-color: navy;
  border-radius: 50%;
}
</style>