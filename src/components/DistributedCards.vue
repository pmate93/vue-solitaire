<template>
    
    <div v-for='(cards, index) in distributedCards' :key="index">
        <div v-if="index > 0" class="card-container"
         @drop="$emit('onDrop', $event, index)"
         @dragenter.prevent
         @dragover.prevent
            >
        <Card v-for='(card, idx) in cards'
         :idx="idx"
         :length="cards.length" 
         :key="card.id" 
         :url="card.src"
         :flipped="card.flipped"
         :style="{top: idx * 12 + 10 + 'px'}"
         :dragging="card.dragging"
         :draggable="card.flipped ? 'true' : 'false'"
         @dragend="$emit('dragEnd', card)"
         @dragstart="$emit('startDrag', $event, card, index)" />

        </div>
    </div>

</template>

<script>
import Card from '../components/Card.vue'

export default {
    name: 'DistributedCards',
    components: {
        Card
    },
    emits:['onDrop', 'startDrag', 'dragEnd'],
    props:{
        distributedCards: Array
    },

    data(){
        return{
            card: '',
            dragging: false,
        }
    },

    methods:{
    }
}
</script>

<style>

.card-container{
    position:relative;
    width:140px;
}


</style>