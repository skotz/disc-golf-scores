<template>
  <div id="app">
    <ScoreList
      scoresData="scores.json"
      playerName="All"
      allowedPlayerNames="Scott,Martin,Tim"
      courseName="All"
      filterCourseHoles="18"
    />
  </div>
</template>

<script>
import ScoreList from "./components/ScoreList.vue";

export default {
  name: "app",
  components: {
    ScoreList
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
