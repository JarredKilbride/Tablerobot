<template>
<div class="row">
 <div class="col flex-jc-c">
    <!-- input/controller. -->
    <Controller class="controller" :robot="robot" @direction="setDirection" @move="move" @report="showReport" @setPosition="place" @updateDir="updateRobotDir"/>
  </div>
  <div class="flex-jc-c col">
   <div class="table-top">
    <Cells v-for="cell in cells" :cell="cell" :robot="robot" :key="cell.key"/>
  </div>
    <!-- cells -->
    <!-- key needs to be changed. -->
  </div>
  <!-- report for robot. -->
  <div v-if="robot.reportActive">
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
    //creating robot object
    let robot = ref({
      x:null,
      y:null,
      facing:'', 
      reportActive:false
    })

    //where the cells and values for them is stored.
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

//emit from controller, changes the robot direction on trun.
let updateRobotDir = (e) => {
    robot.value.facing = e
}

//when user clicks on move buttom emits from controller.
// makes robot it move.
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
}

//this is emited from the controller.vue
//changes the robot obect current facing.
const setDirection = (event)=> {
  robot.value.facing = event.facing
}

//emited from the controller.vue, toggals robot report. by setting the reportActive to true or false
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

  return {cells,robot,setDirection,move,showReport,place,updateRobotDir}
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}

/* class to create the grid */
.table-top {
    display: grid;

    grid-template-columns: repeat(5, 157.7px);
    margin: auto;
    width: 788.5px;
    background-image: url("../src/assets/table-background.png")

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
