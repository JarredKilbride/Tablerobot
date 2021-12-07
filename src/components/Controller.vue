<template>
  <div>
      <h3>Pick your Starting</h3>
      <input type="">
     <button @click.prevent="report()">{{reportButtonText}}</button>
    <button @click.prevent="move()">Move!</button>
    <button @click.prevent="direction({dir:'y', val:1, facing:'North'})" >North</button>
    <button @click.prevent="direction({dir:'x', val:-1, facing:'West'})">West</button>
    <button @click.prevent="direction({dir:'y', val:-1, facing:'South'})">South</button>
    <button @click.prevent="direction({dir:'x', val:1, facing:'East'})">East</button>
    
  </div>
</template>

<script>
import {ref } from 'vue'
export default {

    setup(props,{emit}){
        let reportButtonText = ref('Show Report')
        //triggers the setDirection fuction on the app.vue file.
        function direction(e){
            console.log("test")
            emit('direction',e)
        }
        //triggers the move fuction on the app.vue file. 
        function move(){
            emit('move')
        }

        function report() {
            emit('report')
            //not working
            //  reportButtonText.value = 'Show Report' ? 'Hide Report' : 'Show Report' 
        }



           return {direction,move,report,reportButtonText}
    }
}
</script>

<style>

</style>