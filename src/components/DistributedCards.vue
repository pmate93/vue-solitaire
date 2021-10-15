<template>
    <div class="distributed-cards">
        <div v-for='(cards, index) in distributedCards' :key="index">
            <div v-if="index > 0" class="card-container"
            @drop="$emit('onDrop', $event, index, 'toDist')"
            @dragenter.prevent
            @dragover.prevent
                >
            <Card v-for='(card, idx) in cards'
            :length="cards.length" 
            :key="card.id" 
            :url="card.src"
            :flipped="card.flipped"
            :style="{top: idx * 12 + 10 + 'px'}"
            :dragging="card.dragging"
            :draggable="card.flipped ? 'true' : 'false'"
            @dragend="$emit('dragEnd', card, idx, index, 'fromDist')"
            @dragstart="$emit('startDrag', $event, card, index, 'fromDist')"
            @drag="$emit('onDrag', $event, 'fromDist')"
            @calculateWidth="$emit('calculateWidth', $event)"
            />

            </div>
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
    emits:['onDrop', 'startDrag', 'dragEnd', 'onDrag', 'calculateWidth'],
    props:{
        distributedCards: Array,
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
    height:140px;
}

.distributed-cards{
    display: flex;
}

</style>