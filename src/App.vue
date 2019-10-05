<template>
  <div id="app">
    <h1>Disc Golf Scores</h1>
    <ScoreList
      scoresData="scores.json"
      playerName="Scott"
      allowedPlayerNames="Scott,Martin,Tim"
      courseName="All"
      filterCourseHoles="18"
    />
    <div class="copy">&copy; Scott Clayton &bull; <a href="https://github.com/skotz/disc-golf-scores">GitHub</a></div>
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
  font-family: "Roboto", sans-serif;
  text-align: center;
  margin-top: 50px;
}
#app .copy,
#app .copy a {
  margin-top: 50px;
  color: #ccc;
}
</style>
