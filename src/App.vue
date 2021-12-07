<template>
<div>

  <!-- input/controller. -->
  <Controller @direction="setDirection" @move="move" @report="showReport" @setPosition="place"/>
  <div class="table-top">

    <!-- cells -->
    <!-- key needs to be changed. -->
    <Cells v-for="cell in cells" :cell="cell" :robot="robot" :key="cell.key"/>

  </div>
  {{currentDirection}}
  <!-- report for robot. -->
  <div v-if="robot.reportActive">
  {{robot}}
  </div>
 </div> 
</template>

<script>
import {ref } from 'vue'
import Cells from './components/Cells.vue'
import Controller from './components/Controller'

export default {
  
  name: 'App',
  components: {
    Cells, Controller
  }, 
  setup(){
    //currentDirection the robot is facing
    let currentDirection = ref({
      dir:null,
      val:null})
    //creating robot object
    let robot = ref({
      x:null,
      y:null,
      facing:'', 
      reportActive:false
    })

    let cells = ref([])
//easy for loop to create all the cells obects.
for(let y = 4; y>=0; y--) {
    for(let x = 0; x<5; x++) {
        cells.value.push({
        x:x,
        y: y,
        robotOnSqaure:false,
        // temp
        key: Math.floor(Math.random() * 1000)
        })
    }
}

// when user clicks on move buttom emits this function
//moves the stage direction to the robot and makes it move.
let move = () => {
  //checks to see if the currentDirection y or x aixes
  //once checked looks to see if the total value would be greater the cells on the table. 
  if(currentDirection.value.dir=='y'
   && (robot.value.y+currentDirection.value.val<5 
   && robot.value.y+currentDirection.value.val>-1)){
    robot.value.y += currentDirection.value.val
  }
  else if (currentDirection.value.dir=='x'
   && (robot.value.y+currentDirection.value.val<5 
   && robot.value.y+currentDirection.value.val>-1)){
     robot.value.x += currentDirection.value.val
   }
   else {
     console.log(robot.value.y+currentDirection.value.val)
   }
}

//this emited from the controller.vue
//stages the selected movment.
const setDirection = (event)=> {
  currentDirection.value.val = event.val
  currentDirection.value.dir = event.dir
  robot.value.facing = event.facing
}

//emited from the controller.vue, shows robot report.
const showReport = ()=> {
  if(robot.value.x!==null){
  robot.value.reportActive = !robot.value.reportActive
  }
}

//emit from the controller. user selects the starting cell
const place = (e) => {
  //using parseInt to convert the drop down from string to number
  robot.value.x = parseInt(e.x)
  robot.value.y = parseInt(e.y)
}

  return {cells,robot,setDirection,move,currentDirection,showReport,place}
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.table-top {
    display: flex;
    flex-wrap: wrap;
    width: 400px;
    margin: auto;
    /* height: 204px; */
  
}

</style>
