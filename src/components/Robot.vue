<template>
<div>
    <img class="table-robot" :class="robtTurn" src="../assets/topdown-cosmonaut.svg" alt="Robot Photo">
</div>
</template>

<script>
import {
    computed
} from 'vue'
export default {
    props: ['robot'],
    name: 'robot',
    setup(props) {

        //computed value to change the class name based on the facing direction
        //passes the from object from cell>app object.
        const robtTurn = computed(() => {
            return 'facing-' + props.robot.facing
        })
        return {
            robtTurn
        }
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
.facing-EAST {
    transform: rotate(90deg);
}

.facing-WEST {
    transform: rotate(270deg);
}

.facing-SOUTH {
    transform: rotate(180deg);
}

 @media only screen and (max-width:480px) { 
  .table-robot {
    width: 50px;
    height: 50px;
}
}
</style>
