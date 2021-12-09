<template>
  <div>
      <img class="table-robot" :class="robtTurn" src="../assets/topdown-cosmonaut.svg" alt="Robot Photo">
  </div>
</template>
 
<script>
import { computed } from 'vue'
export default {
props:['robot'],
setup(props){

  //computed class to change the class based on the facing direction
  const robtTurn = computed(()=>{
    return 'facing-' + props.robot.facing
  })

  return {robtTurn}
}
}


</script>

<style>
.table-robot {
  width: 150px;
  height: 150px;
}

/* changes the robots directions */
.facing-East{
  transform: rotate(90deg);
}
.facing-West{
  transform: rotate(270deg);
}
.facing-South{
    transform: rotate(180deg);
}


</style>