<template>
  <div class="cell">
      <p></p>
      {{robotActive}}
      <!-- <div v-if="robotActive">
          {{robot.y}}
      </div> -->
      <!-- {{cell.x}} -->
  </div>
</template>

<script>
import {computed, ref } from 'vue'
export default {
    props: ['cell','robot'],
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
    width: 80px;
    height: 64px;
    border: 2px solid rgb(75,75,75);
    
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>