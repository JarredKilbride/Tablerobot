<template>
<div class="row">
 <div class="col flex-jc-c">
    <!-- input/controller. -->
    <Controller class="controller" :robot="robot" @direction="setDirection" @move="move" @report="showReport" @setPosition="place" @updateDir="updateRobotDir"/>
  </div>
  <div class="flex-jc-c table-top col">
   
    <Cells v-for="cell in cells" :cell="cell" :robot="robot" :key="cell.key"/>
    <!-- cells -->
    <!-- key needs to be changed. -->
  </div>
  <!-- {{currentDirection}} -->
  <!-- report for robot. -->
  <div v-if="robot.reportActive">
  <!-- {{robot}} -->
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
    //not needed
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

let updateRobotDir = (e) => {
    robot.value.facing = e
}

// when user clicks on move buttom emits this function
//moves the stage direction to the robot and makes it move.
let move = (e) => {
  //checks to see if the currentDirection y or x aixes
  //once checked looks to see if the total value would be greater the cells on the table. 
  if(e.dir=='y'
   && (robot.value.y+e.val<5 
   && robot.value.y+e.val>-1)){
    robot.value.y += e.val
  }
  else if (e.dir=='x'
   && (robot.value.x+e.val<5 
   && robot.value.x+e.val>-1)){
     robot.value.x += e.val
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
  robot.value.facing = e.facing

}

  return {cells,robot,setDirection,move,currentDirection,showReport,place,updateRobotDir}
  }
}

</script>

<style>
body {
  background:black
}



#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #ffffffCC;
  margin-top: 60px;
}

/* class to create the grid */
.table-top {
    display: grid;
    grid-template-columns: repeat(5, 100px);
    margin: auto;
    /* height: 204px; */
}

.flex-jc-c{
 justify-content: center;
}


 .controller {
   width: 700px;
   margin:auto; 
 }

</style>
