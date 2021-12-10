<template>
<!-- select starting spot -->
  <div class="Panels">
      <section class="starting-input bg-secondary">
      <h3 class="panel-header">Pick your Starting Position</h3>
      <!-- hiden text but shows if a user trys to submit with  no values. -->
      <p class="text-error" v-if="axis.error" >Error! you must fill in all feilds</p>
      <div class="starting-inner">
          <div class="col">
            <label> <b> X : </b></label>
            <select v-model="axis.x" class="form-select" aria-label="X axis select">
                <option value=0>0</option>
                <option value=1>1</option>
                <option value=2>2</option>
                <option value=3>3</option>
                <option value=4>4</option>
            </select>
        </div>
        <div class="col">
            <label> <b> Y : </b> </label>
            <select v-model="axis.y" class="form-select" aria-label="Y axis select">
                <option value=0>0</option>
                <option value=1>1</option>
                <option value=2>2</option>
                <option value=3>3</option>
                <option value=4>4</option>
            </select>
        </div>
        <div class="col">
            <label> <b> Facing :</b></label>
            <select v-model="axis.facing" class="form-select" aria-label="Y axis select">
                <option value="North">North</option>
                <option value="East">East</option>
                <option value="South">South</option>
                <option value="West">West</option>
            </select>
            </div>
        </div>
            <button  class="place-button btn btn-action-primary
" @click.prevent="userSetPosition">Place!</button>
    </section>

    <!-- UI Controller -->
    <section class="bg-secondary">
        <h3 class="panel-header">Controlls</h3>
        <div class="controls">
                <button class="btn btn-action-secondary" @click.prevent="trunLeft">Left</button>
        <button class="btn btn-action-secondary" @click.prevent="trunRight">Right</button>
        <button class="btn btn-action-primary" @click.prevent="move()">Move!</button>
        </div>
    </section>

    <!-- 3rd panel-->
    <section class="bg-secondary">
        <div class="row">
            <!-- mass input -->
        <div class="col mass-input">
        <h3 class="panel-header">Mass input</h3>
        <p>Stage a action by pressing enter <br>
            Vaild commands: Move,Right,and Left. 
        </p>
        <input v-model="input" type="text" @keydown.enter.prevent="handleKeyDown" class="form-control">
        <div class="mass-input-textarea bg-primary">
           <P v-for="action in multInput" :key="action">C:\user\System\Actions\{{action}} </P>
        </div>
        <button class="btn btn-action-primary" @click.prevent="massInputMove">Run Input</button>
        </div>

        <!-- report -->
        <div class="col report-display">
            <RobotReport v-if="robot.reportActive" @reportClose="report" :robot="robot"/>
            <!-- apply v-else to remove the button when report is active. -->
            <button v-else class="btn btn-action-primary" @click.prevent="report()">Report</button>
        </div>
        </div>
    </section>
  </div>
</template>

<script>
import {ref } from 'vue'
import RobotReport from './RobotReport.vue'
export default {
    props:['robot'],
    name:'control',
    components: {
        RobotReport
    }, 
    setup(props,{emit}){

        //this is all the values of what the n,s,e,w
        let dirVal = ref({
            North:{
                    val: 1,
                    dir: "y",
                    facing: "North"
                },
            South:{
                    val: -1,
                    dir: "y",
                    facing: "South"
                },
            East:{
                    val: 1,
                    dir: "x",
                    facing: "East"
                }, 
            West:{
                    val: -1,
                    dir: "x",
                    facing: "West"
                }         
        })

        //mass input var.
        let input = ref('')
        // staging var for the mass input.
        let multInput = ref([])

        //input var for the place component
        let axis = ref({
            x:'',
            y:'', 
            facing:"", 
            error:false
        })

        //stageing var for moving.
        let turn = ref({
                val: 0,
                dir: "x",
                facing: ""
        })

    //this is handles the multple inputed by the user
    const handleKeyDown = () => {
        input.value = input.value.replace(/\s/,'') //removes white space
        let upperCase = input.value.toUpperCase() //coverts everything to upper case
        //checks to make sure inputs enters are valid.
        if(upperCase =="RIGHT" || upperCase =="LEFT" || upperCase =="MOVE"){
            //pushes to array
            multInput.value.push(upperCase)
        }
        //sets the input feild blank for the use dosent need to delete the previous entry.
        input.value = ''
    }

    //run the mass inputs.
    function massInputMove (){
        //checks to see what commands they are and run function based on them.
        multInput.value.map(data => {
            switch(data){
                case 'LEFT':
                    trunLeft()
                break;
                case 'RIGHT':
                    trunRight()
                break;
                default:
                    move()    
            }
        })
        multInput.value = []
    }

    //when called, will check what the current direction the robot is facing and trun it right
    function trunRight(){
            switch (turn.value.facing) {
            case 'North':
                turn.value = dirVal.value.East
                break;
            case 'East':
                turn.value = dirVal.value.South
                break;
            case 'South':
                turn.value = dirVal.value.West
                break;
            default:
                turn.value = dirVal.value.North
            }
            emit('updateDir',turn.value.facing)
        }

    //when called, will check what the current direction the robot is facing and trun it left
    function trunLeft(){
            switch (turn.value.facing) {
            case 'North':
                turn.value = dirVal.value.West
                break;
            case 'West':
                    turn.value = dirVal.value.South
                break;
            case 'South':
                    turn.value = dirVal.value.East
                break;
            default:
                turn.value = dirVal.value.North
            }
            emit('updateDir',turn.value.facing)
        }
        

        //triggers the setDirection fuction on the app.vue file.
        function direction(e){
            emit('direction',e)

        }
        //triggers the move fuction on the app.vue file. 
        function move(){
            emit('move',turn.value)
        }

        //show robot report and emits function on app.vue to change reportActive to true.
        function report() {
            emit('report')
        }

        //emits function place on app.vue and sets the robots Position
        function userSetPosition(){
            // checks to see if the facing input is blank if so defaults it to north

            if(!axis.value.facing ||!axis.value.x || !axis.value.y) {
                //if anything is blank on the place input display error for user.
                axis.value.error = true
                } else {
                    axis.value.error = false
                emit('setPosition',axis.value)
                //checks the suer place input facing direction. 
                //based on what it is, it will set the staging var turn to the value north,south,west, or east.
                for(let key in dirVal.value) {
                    if(key == axis.value.facing) {
                        turn.value = dirVal.value[key]
                    }
                }
                }
                //set the inputs to blank when place button is pressed.
                axis.value.y = ''
                axis.value.x = ''
                axis.value.facing = '' 

        }



           return {direction,move,report,axis,userSetPosition,trunRight,trunLeft,turn,input,multInput,handleKeyDown,massInputMove}
    }
}
</script>

<style>

.starting-inner{
    display: flex;
    /* display: flex; */
}

/* adding margin at the top of the button to give it some space */
.place-button{
    margin:15px auto;
}

/* adding spacing to the top and rounding the corners. */
.controller section {
    padding: 10px;
    margin-top: 50px;
    border-radius: 20px;
}
/* but not giving margin to the top panel for it is on the top. */
.controller section:first-child {
    padding: 10px;
    margin-top: 0;
    border-radius: 20px;
}

/* spacing on the UI buttons */
.controls {
    display: flex;
    justify-content: space-around;
}

.btn {
    width: 150px;
}

/* give the header some room on the top */
.control h3 {
    margin-top:105px;
}

.mass-input input {
    width: 90%;
    margin:auto;
}

/* making items stack on each other and setting the width */
.mass-input {
  display: flex;
  flex-direction: column;
  width: 90%;
}

/* centing the button */
.mass-input button{
margin:auto;
}

/* this is the showing the user the stage area. */
.mass-input-textarea {
    width: 300px;
    height: 200px;
    margin:10px auto;
    border:1px black solid;
    border-radius: 20px;
    text-align: left;
    overflow: scroll
}
/* moving the text away from the edge */
.mass-input-textarea p {
    padding-left: 5px;
}

/* helps center items */
.report-display {
    display: flex;
}

/* centering the button */
.report-display button{
    margin: auto;
}



</style>