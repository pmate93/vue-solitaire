<template>
  <img v-if="flipped" 
  v-bind:src="this.url"
  :style="{display: this.dragging ? 'none' : ''}"

  class="child">

  <img v-else v-bind:src="card_back" class="child">
</template>

<script>
import card_back from '../assets/card-back.jpg'

export default {
    name: 'Card',
    props:{
      url: String,
      length: Number,
      idx: Number,
      flipped: Boolean,
      dragging: Boolean
    },

    data(){
      return{
        card_back: card_back
      }
    }


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