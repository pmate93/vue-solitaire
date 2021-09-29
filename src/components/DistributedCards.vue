<template>
<Card />
  
<div v-for='(cards, index) in distributedCards' :key="index">
    <Card v-for='card in cards' :key="card.id" :title="card.type" />


</div>

</template>

<script>
import Card from '../components/Card.vue'

export default {
    name: 'DistributedCards',
    components: {
        Card
    },
    props:{
        distributedCards: Array
    },

    data(){
        return{
            card: ''
        }
    },

    methods:{
        
    }
}
</script>

<style>

</style>