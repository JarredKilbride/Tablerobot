<template>
  <div class="Panels">
      <section class="starting-input bg-secondary">
      <h3>Pick your Starting Position</h3>
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
            <button  class="place-button btn btn-danger
" @click.prevent="userSetPosition">Place!</button>
    </section>
    <section class="bg-secondary">
        <h3>Controlls</h3>
        <div class="controls">
                <button class="btn btn-danger" @click.prevent="trunLeft">Left</button>
        <button class="btn btn-danger" @click.prevent="trunRight">Right</button>
        <button class="btn btn-danger" @click.prevent="move()">Move!</button>
        </div>
    </section>
    <!-- {{turn}} -->

    <section class="bg-secondary">
        <div class="row">
        <div class="col mass-input">
        <h3>Mass input</h3>
        <p>Submit a action by pressing enter</p>
        <input v-model="input" type="text" @keydown.enter.prevent="handleKeyDown" class="form-control">
        <div class="mass-input-textarea">
            {{multInput}}
        </div>
        <button class="btn btn-danger" @click.prevent="massInputMove">Run Mass input</button>
        </div>
        <div class="col report-display">
            <RobotReport v-show="robot.reportActive" :robot="robot"/>
            <button class="btn btn-danger" @click.prevent="report()">Report</button>
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


        let input = ref('')
        let multInput = ref([])

        //input for the place componet
        let axis = ref({
            x:0,
            y:0, 
            facing:""
        })

        //state for the current dir
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
        //sets the input feild to be place for the user.
        input.value = ''
    }

    function massInputMove (){
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
            if(axis.value.facing== '') {
                axis.value.facing='North'
                emit('setPosition',axis.value)
                for(let key in dirVal.value) {
                    if(key == axis.value.facing) {
                        turn.value = dirVal.value[key]
                    }
                }
                } else {
                emit('setPosition',axis.value)
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
.starting-inner .col {
    margin: 0 10px;
}

.place-button{
    margin:15px auto;
}

section {
    padding: 10px;
    margin-top: 50px;
    border-radius: 20px;
    /* border:white 7px solid; */
    /* box-shadow: rgba(173, 173, 173, 0.2) 0px 7px 29px 0px;  */
}
section:first-child {
    padding: 10px;
    margin-top: 0;
    border-radius: 20px;
}

.controls {
    display: flex;
    justify-content: space-around;
}
.btn {
    width: 150px;
}

 h3 {
    margin-top:105px;
}

.mass-input {
  display: flex;
  flex-direction: column;
}

.mass-input button, .mass-input input{
margin:auto;
}

.mass-input-textarea {
    width: 300px;
    height: 200px;
    margin:10px auto;
    border:1px black solid;
    border-radius: 20px;
    background:rgb(156, 21, 21)
}

.report-display {
    display: flex;
    flex-direction: column;
}
.report-display button{
    margin: auto;
    align-self: end;
}

</style>