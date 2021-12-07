<template>
  <div>
      <div class="starting-input">
      <h3>Pick your Starting Position</h3>
      <div class="starting-inner">
          <h3>X :</h3>
        <select v-model="axis.x" class="form-select" aria-label="X axis select">
        <option selected> X Axis</option>
        <option value=0>0</option>
        <option value=1>1</option>
        <option value=2>2</option>
        <option value=3>3</option>
        <option value=4>4</option>
        </select>
        <h3>Y :</h3>
<select v-model="axis.y" class="form-select" aria-label="Y axis select">
        <option selected> Y Axis</option>
        <option value=0>0</option>
        <option value=1>1</option>
        <option value=2>2</option>
        <option value=3>3</option>
        <option value=4>4</option>
        </select>
        <button @click.prevent="userSetPosition">Place</button>
        </div>
    </div>
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

        let axis = ref({
            x:0,
            y:0
        })

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

        function userSetPosition(){
                emit('setPosition',axis.value)
        }



           return {direction,move,report,reportButtonText,axis,userSetPosition}
    }
}
</script>

<style>
.starting-inner{
    display: flex;
}
</style>