<template>
  <div class="cell">
        <Robot :robot="robot" v-if="robotActive" />
      <!-- <div v-if="robotActive">
          {{robot.y}}
      </div> -->
      <!-- {{cell.x}} -->
  </div>
</template>

<script>
import {computed, ref } from 'vue'
import Robot from '../components/Robot'
export default {
    props: ['cell','robot'],
        components: {
        Robot
    }, 
    // props:[cell]
    setup(props){
        //need to rename
        let cells = ref(props.cell)
        // let robot = ref(props.robot)

    const robotActive = computed(()=>{
        if(props.cell.x == props.robot.x && props.cell.y == props.robot.y){
            return true
        }
        else {
            return false
        }
    })
        return {cells,robotActive}
    },


}
</script>

<style>
 .cell {
    width: 157.7px;
    height: 157.7px;
    border: 2px solid rgb(75,75,75);
    
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>