<template>

  <img v-if="flipped" 
  v-bind:src="this.url"
  ref="image"
  @load="getWidth"
  :style="{display: this.dragging ? 'none' : ''}"

  class="child">

  <img v-else v-bind:src="card_back" class="child">

</template>

<script>
import card_back from '../assets/card-back.jpg'
export default {
  name: 'Card',
  emits: ['calculateWidth'],
  props:{
    url: String,
    length: Number,
    idx: Number,
    flipped: Boolean,
    dragging: Boolean,

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

      this.imageSize.height = this.$refs.image.clientHeight;
      this.imageSize.width = this.$refs.image.clientWidth;

      this.$emit('calculateWidth', this.imageSize);
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
  width: 12vh;
  opacity:100%;
}
/* .child:last-child{
  position: relative;
} */
img.dragging {
  background-color: navy;
  border-radius: 50%;
}
</style>