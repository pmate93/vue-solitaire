<template>
  <div v-for='(cards, index) in finalCards' :key="index" 
  class="card-form" 
  :style="{width: this.cardWidth + 'px', height: this.cardHeight + 'px'}"
  @drop="$emit('onDrop', $event, index, 'toFinal')"
  @dragenter.prevent
  @dragover.prevent >

    <div v-if="cards.length === 1" ></div>
    <Card v-else v-for='(card) in cards' 
    :key="card.id"
    :flipped="card.flipped"
    :url="card.src"
    :dragging="card.dragging"
    @dragend="$emit('dragEnd', card, cards.length - 1, index, 'fromFinal')"
    @dragstart="$emit('startDrag', $event, card, index, 'fromFinal')"
    @drag="$emit('onDrag', $event)" />

  </div>
</template>

<script>
import Card from '../components/Card.vue';

export default {
    name: 'FinalContainer',
    emits: ['onDrop', 'dragEnd', 'startDrag', 'onDrag'],
    components:{
        Card
    },
    props:{
        finalCards: Array,
        cardWidth: Number,
        cardHeight: Number
    },
}
</script>

<style>

.card-form{
    background-color:rgb(60, 124, 196);
    border-radius: 5px;
    margin: 1%;
}

</style>