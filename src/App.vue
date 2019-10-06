<template>
  <div id="app">
    <b-navbar type="dark" variant="success">
      <b-navbar-brand>Disc Golf Scores</b-navbar-brand>
      <b-navbar-nav class="ml-auto">
        <b-nav-text right>Scott Clayton</b-nav-text>
      </b-navbar-nav>
    </b-navbar>
    <ScoreList
      scoresData="scores.json"
      playerName="Scott"
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
  font-family: "Roboto", sans-serif;
  text-align: center;
  margin-bottom: 25px;
}
nav {
  margin-bottom: 25px;
}
</style>
