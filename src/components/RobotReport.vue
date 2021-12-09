<template>
  <div class="robot-report">
        <p>X : {{robot.x}}</p>
        <p>Y : {{robot.y}}</p>
        <p>Facing : {{robot.facing}}</p>
  </div>
</template>

<script>
export default {
props:['robot']
}
</script>

<style>
.robot-report {
    display: flex;
    flex-direction: column;
}
</style>