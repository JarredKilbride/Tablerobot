<template>
  <div>
      <img class="table-robot" :class="robtTurn" src="../assets/topdown-cosmonaut.svg" alt="Robot Photo">
  </div>
</template>
 
<script>
import { computed } from 'vue'
export default {
props:['robot'],
name:'robot',
setup(props){

  //computed value to change the class name based on the facing direction
  //passes the from object from cell>app object.
  const robtTurn = computed(()=>{
    return 'facing-' + props.robot.facing
  })
  return {robtTurn}
}
}


</script>

<style>
/* setting the robot size */
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