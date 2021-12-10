<template>
  <div class="robot-report">
        <p><B>X :</B> {{robot.x}}</p>
        <p><B>Y :</B> {{robot.y}}</p>
        <p><B>F :</B> {{robot.facing}}</p>
        <button @click.prevent="reportClose" class=" btn btn-action-primary">Close</button>
  </div>
</template>

<script>
export default {
props:['robot'],
name:'RobotReport',
setup(props,{emit}){
    //emits to controller > app to toggal the reportActive in robot object.
    const reportClose = () => {
        emit('reportClose')
        }

        return {reportClose}
    }
}
</script>

<style>
/* setting up the report view*/
.robot-report {
    display: flex;
    flex-direction: column;
    margin: auto;
    padding: 40px 30px;
    border-radius: 20px;
    min-width: 192px;
    font-size: 30px;
    box-shadow: rgba(50, 50, 93, 0.25) 0px 6px 12px -2px, rgba(0, 0, 0, 0.3) 0px 3px 7px -3px;
    background-color: rgba(255, 255, 255, 0.16);
}

</style>