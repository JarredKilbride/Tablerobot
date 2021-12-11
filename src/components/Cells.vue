<template>
<div class="cell">
    <!-- checks the robot active computed value and if true it shows the robot. -->
    <Robot :robot="robot" v-if="robotActive" />
</div>
</template>

<script>
import {
    computed,
    ref
} from 'vue'
import Robot from '../components/Robot'
export default {
    props: ['cell', 'robot'],
    name: 'name',
    components: {
        Robot
    },
    setup(props) {
        let cells = ref(props.cell)

        // this is checking the where the robot is and if it matches the cell it returns true.
        const robotActive = computed(() => {
            if (props.cell.x == props.robot.x && props.cell.y == props.robot.y) {
                return true
            } else {
                return false
            }
        })
        return {
            cells,
            robotActive
        }
    },

}
</script>

<style>
.cell {
    /* creating the cell. */
    display: flex;
    align-items: center;
    justify-content: center;
    /* **important** the width here must match the secound argument from table-top class on app.vue */
    /* css feild name : grid-template-columns: repeat(5, 157.7px <- this must be the same) */
    width: 157.7px;
    height: 157.7px;
    border: 2px solid rgba(75, 75, 75, 0.253);
}

/* for mobile view makeing the cells smaller */
 @media only screen and (max-width:480px) { 
  .cell {
    width: 60px;
    height: 60px;
  }
}
</style>
