<template>
<!-- select starting spot -->
<div>
    <section class="starting-input bg-secondary">
        <h3 class="panel-header">Starting Position</h3>
        <!-- hiden text but shows if a user trys to submit with  no values. -->
        <p class="text-error" v-if="axis.error">Error! you must fill in all feilds</p>
        <div class="starting-inner row gx-1">
            <div class="col">
                <label class="form-label"> <b> X : </b></label>
                <select v-model="axis.x" class="form-select" aria-label="X axis select">
                    <option value=0>0</option>
                    <option value=1>1</option>
                    <option value=2>2</option>
                    <option value=3>3</option>
                    <option value=4>4</option>
                </select>
            </div>
            <div class="col">
                <label class="form-label"> <b> Y : </b> </label>
                <select v-model="axis.y" class="form-select" aria-label="Y axis select">
                    <option value=0>0</option>
                    <option value=1>1</option>
                    <option value=2>2</option>
                    <option value=3>3</option>
                    <option value=4>4</option>
                </select>
            </div>
            <div class="col">
                <label class="form-label"> <b> Facing :</b></label>
                <select v-model="axis.facing" class="form-select" aria-label="Facing select">
                    <option value="NORTH">North</option>
                    <option value="EAST">East</option>
                    <option value="SOUTH">South</option>
                    <option value="WEST">West</option>
                </select>
            </div>
        </div>
        <button class="place-button btn btn-action-primary
" @click.prevent="userSetPosition">Place</button>
    </section>

    <!-- UI Controller -->
    <section class="bg-secondary">
        <h3 class="panel-header">Controlls</h3>
        <div class="controls">
            <button class="btn btn-action-primary" @click.prevent="trunLeft">Left</button>
            <button class="btn btn-action-primary" @click.prevent="trunRight">Right</button>
            <button class="btn btn-action-primary" @click.prevent="move()">Move</button>
        </div>
    </section>

    <!-- 3rd panel-->
    <section class="bg-secondary">
        <div class="row">
            <!-- mass input -->
            <div class="col mass-input">
                <h3 class="panel-header">Bulk Update</h3>
                <p>Stage a action by pressing enter. <br>
                    Vaild commands: Move, Right, Left, Report, and place x,y,f
                </p>
                <textarea v-model="multInput" class="bulk-input-textarea form-control">
        </textarea>
                <button class="btn btn-action-primary" @click.prevent="massInputMove">Run Bulk Update</button>
            </div>
            <!-- report -->
            <div class="col report-display">
                <RobotReport v-if="robot.reportActive" @reportClose="report" :robot="robot" />
                <!-- apply v-else to remove the button when report is active. -->
                <button v-else class="btn btn-action-primary" @click.prevent="report()">Report</button>
            </div>
        </div>
    </section>
</div>
</template>

<script>
import {
    computed,
    ref
} from 'vue'
import RobotReport from './RobotReport.vue'
export default {
    props: ['robot'],
    name: 'control',
    components: {
        RobotReport
    },
    setup(props, {
        emit
    }) {

        //this is all the values of what the n,s,e,w
        let dirVal = ref([{
                val: 1,
                dir: "y",
                facing: "NORTH"
            },
            {
                val: -1,
                dir: "y",
                facing: "SOUTH"
            },
            {
                val: 1,
                dir: "x",
                facing: "EAST"
            },
            {
                val: -1,
                dir: "x",
                facing: "WEST"
            }
        ])

        // staging var for the mass input.
        let multInput = ref('')

        //input var for the place component
        let axis = ref({
            x: null,
            y: null,
            facing: "",
            error: false
        })

        //stageing var for moving.
        let turn = ref({
            val: "",
            dir: "",
            facing: ""
        })

        // used to validate use input on bulk input.
        function validateDir(dir) {
            let v = ''
            dirVal.value.map(data => {
                if (data.facing == dir) {
                    v = true
                }
            })
            return v
        }

        //run the mass inputs.
        function massInputMove() {
            //checks to see what commands they are and run function based on them.
            textareinput.value.map(data => {
                //check to see if the bulk input has the word place
                if (data.toUpperCase().includes("PLACE")) {
                    // covert everything to uppercase than remove place and white space. Then it makes it into an array.
                    let placecommand = data.toUpperCase().replace('PLACE', '').replace(/\s/, '').split(",");
                    //checking to make sure all values meet the requirements
                    if (
                        placecommand.length == 3 &&
                        parseInt(placecommand[0]) >= 0 && parseInt(placecommand[0]) <= 4 &&
                        parseInt(placecommand[1]) >= 0 && parseInt(placecommand[1]) <= 4 &&
                        validateDir(placecommand[2])
                    ) {
                        setFacingDirection(placecommand[2])
                        // sets everything in the staging area. 
                        let stage = {
                            x: parseInt(placecommand[0]),
                            y: parseInt(placecommand[1]),
                            facing: placecommand[2],
                        }
                        // sets everything in the staging area. 
                        //emits the direction and cell value to the robot.
                        emit('setPosition', stage)
                    }
                } else {
                    //removing all whitespace and conveting everything to upper case
                    let input = data.replace(/\s/, '').toUpperCase()
                    // based on input runs function.
                    console.log(input)
                    switch (input) {
                        case 'LEFT':
                            trunLeft()
                            break;
                        case 'RIGHT':
                            trunRight()
                            break;
                        case 'MOVE':
                            move()
                            break;
                        case 'REPORT':
                            report()
                            break;
                    }
                }
            })
            //clear the bulk input text feild when everything as been ran.
            multInput.value = []
        }

        //converts the bulk input in a array
        const textareinput = computed(() => {
            let item = multInput.value.split('\n')
            return item
        })

        //when called, will check what the current direction the robot is facing and trun it right
        function trunRight() {
            switch (turn.value.facing) {
                case 'NORTH':
                    turn.value = dirVal.value[2]
                    break;
                case 'EAST':
                    turn.value = dirVal.value[1]
                    break;
                case 'SOUTH':
                    turn.value = dirVal.value[3]
                    break;
                case 'WEST':
                    turn.value = dirVal.value[0]
                    break;
            }
            emit('updateDir', turn.value.facing)
        }

        //when called, will check what the current direction the robot is facing and trun it left
        function trunLeft() {
            switch (turn.value.facing) {
                case 'NORTH':
                    turn.value = dirVal.value[3]
                    break;
                case 'WEST':
                    turn.value = dirVal.value[1]
                    break;
                case 'SOUTH':
                    turn.value = dirVal.value[2]
                    break;
                case 'EAST':
                    turn.value = dirVal.value[0]
                    break;
            }
            emit('updateDir', turn.value.facing)
        }

        //triggers the setDirection fuction on the app.vue file.
        function direction(e) {
            emit('direction', e)

        }
        //triggers the move fuction on the app.vue file. 
        function move() {
            emit('move', turn.value)
        }

        //show robot report and emits function on app.vue to change reportActive to true.
        function report() {
            emit('report')
        }
        //when called takes in a argument and sets the trun value based on a match. eg norh,south,east, and west.
        let setFacingDirection = (e) => {
            dirVal.value.map(dir => {
                if (dir.facing == e) {
                    turn.value = dir
                }
            })
        }

        //emits function place on app.vue and sets the robots Position
        function userSetPosition() {
            // checks to see if the facing input is blank if so defaults it to north
            if (axis.value.facing == '' || axis.value.x == '' || axis.value.y == '') {
                //if anything is blank on the place input display error for user.
                axis.value.error = true
            } else {
                axis.value.error = false
                emit('setPosition', axis.value)
                //checks the suer place input facing direction. 
                //based on what it is, it will set the staging var turn to the value north,south,west, or east.
                setFacingDirection(axis.value.facing)
            }
            //set the inputs to blank when place button is pressed.
            axis.value.y = ''
            axis.value.x = ''
            axis.value.facing = ''

        }

        return {
            direction,
            move,
            report,
            axis,
            userSetPosition,
            trunRight,
            trunLeft,
            turn,
            multInput,
            massInputMove,
            textareinput
        }
    }
}
</script>

<style>
.starting-inner {
    display: flex;
    /* display: flex; */
}

/* adding margin at the top of the button to give it some space */
.place-button {
    margin: 15px auto;
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
    margin-top: 105px;
}

/* making items stack on each other and setting the width */
.mass-input {
    display: flex;
    flex-direction: column;
    width: 90%;
}

/* centing the button */
.mass-input button {
    margin: auto;
}

/* this is the showing the user the stage area. */
.bulk-input-textarea {
    width: 300px;
    height: 200px;
    margin: 10px auto;
    padding-left: 15px;
    border: 1px black solid;
    border-radius: 20px;
    text-align: left;
    resize: none;
    overflow: scroll
}

/* helps center items */
.report-display {
    display: flex;
}

/* centering the button */
.report-display button {
    margin: auto;
}

 @media only screen and (max-width:480px) { 
 .controller section:last-child {
    margin-bottom: 50px;
}
}
</style>
