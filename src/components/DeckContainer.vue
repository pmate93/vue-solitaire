<template>
    <div class="deck">
        <div class="deck-container" @click="this.isDeckEmpty ? $emit('cardsBackToDeck') : null">
            <div v-for="(card, index) in deck" :key="card.id">
                <Card
                class="card"
                :style="{left: index * 3 + 10 + 'px'}"
                :draggable="false"
                :flipped="card.flipped"
                :isDeckEmpty="isDeckEmpty"
                :url="card.src"
                :dragging="card.dragging"
                @click="index === deck.length - 1 ? $emit('flipThreeCards') : null"
                />
                
            </div>
        </div>
        <div class="deck-container">
            <div v-for="(card, index) in topThreeCards" :key="card.id">
                <Card
                class="card"
                :style="{left: index * 15 + 10 + 'px'}"
                :draggable="index == topThreeCards.length - 1 ? true : false"
                :flipped="card.flipped"
                :isDeckEmpty="isDeckEmpty"
                :url="card.src"
                :dragging="card.dragging"
                @dragend="$emit('dragEnd', card, index, -1, 'fromDeck')"
                @dragstart="$emit('startDrag', $event, card, -1, 'fromDeck')"
                @drag="$emit('onDrag', $event, 'fromDeck')"
                
                />
                
            </div>
        </div>

    </div>
</template>

<script>
import Card from '../components/Card.vue';

export default {
    name: 'DeckContainer',
    emits: ['flipThreeCards', 'cardsBackToDeck', 'dragEnd', 'startDrag', 'onDrag'],
    components:{
        Card
    },
    props:{
        deck: Array,
        cardsFlippedFromDeck: Array,
        isDeckEmpty: Boolean,
        topThreeCards: Array
    },
}
</script>

<style scoped>

.deck-container{
    position:relative;
    width:200px;
    min-height: 200px;
}

/* .card{
} */

.deck{
    display: flex;
    
}

</style>