<template>
  <div>
    <div class="filters">
      <div>
        Filter Players
        <select v-model="playerName">
          <option
            v-for="name in this.allPlayerNames"
            v-bind:value="name"
            v-bind:key="name"
          >{{ name }}</option>
        </select>
      </div>
      <div>
        Filter Courses
        <select v-model="courseName">
          <option
            v-for="name in this.allCourseNames"
            v-bind:value="name"
            v-bind:key="name"
          >{{ name }}</option>
        </select>
      </div>
    </div>
    <div class="header">
      <div class="player">Player</div>
      <div class="course">Course</div>
      <div v-for="hole in this.numberOfHoles" v-bind:key="hole" class="hole">{{ hole }}</div>
      <div class="par" v-on:click="sortByPar">+/-</div>
      <div class="date" v-on:click="sortByDate">Date</div>
    </div>
    <div v-for="score in this.filteredScores" v-bind:key="score.id">
      <div class="player">{{ score.PlayerName }}</div>
      <div class="course">{{ score.CourseName }}</div>
      <div
        v-for="hole in score.Holes"
        v-bind:key="hole.id"
        class="hole"
        :class="[ 'par' + hole.OverUnder ]"
      >{{ hole.Score }}</div>
      <div class="par">{{ score.OverUnderPar }}</div>
      <div class="date">{{ score.Date.toLocaleDateString("en-US") }}</div>
    </div>
  </div>
</template>

<script>
const csv = require("csvtojson");
export default {
  props: {
    scoresData: String,
    playerName: String,
    allowedPlayerNames: String,
    courseName: String,
    filterCourseHoles: String
  },
  data() {
    return {
      publicPath: process.env.BASE_URL,
      scores: [],
      allPlayerNames: ["All"],
      allCourseNames: ["All"],
      allHoleCounts: [],
      sortPlayer: "",
      sortCourse: "",
      sortPar: "",
      sortDate: "desc"
    };
  },
  mounted: function() {
    fetch(this.publicPath + this.scoresData)
      .then(response => response.json())
      .then(data => (this.scores = data))
      .catch(error => console.error(error));
  },
  methods: {
    resetSort: function() {
      this.sortPlayer = "";
      this.sortCourse = "";
      this.sortPar = "";
      this.sortDate = "desc";
    },
    sortByPar: function() {
      this.sortPlayer = "";
      this.sortCourse = "";
      this.sortPar = this.sortPar == "asc" ? "desc" : "asc";
      this.sortDate = "";
    },
    sortByDate: function() {
      this.sortPlayer = "";
      this.sortCourse = "";
      this.sortPar = "";
      this.sortDate = this.sortDate == "asc" ? "desc" : "asc";
    }
  },
  computed: {
    numberOfHoles: function() {
      if (typeof this.filterCourseHoles === "undefined") {
        return 0;
      }
      return parseInt(this.filterCourseHoles, 10);
    },
    getSortPar: function () {
      return this.sortPar;
    },
    getSortDate: function () {
      return this.sortDate;
    },
    filteredScores: function() {
      let filtered = [];
      for (let i = 0; i < this.scores.length; i++) {
        // Get a list of the scores for each hole
        var holes = [];
        for (
          let h = 1;
          h <= 100 &&
          typeof this.scores[i]["Hole" + h] !== "undefined" &&
          this.scores[i]["Hole" + h] != 0;
          h++
        ) {
          var currentScore = this.scores[i];
          var score = parseInt(this.scores[i]["Hole" + h], 10);
          var par = parseInt(
            this.scores.find(function(e) {
              // UDisc stores par for a course as a separate player named "Par"
              return (
                e.PlayerName == "Par" &&
                e.CourseName == currentScore.CourseName &&
                e.Date == currentScore.Date
              );
            })["Hole" + h],
            10
          );
          holes.push({
            Number: h,
            Score: score,
            Par: par,
            OverUnder: score - par
          });
        }
        // Save a list of all the player names
        if (
          this.allPlayerNames.indexOf(this.scores[i].PlayerName) < 0 &&
          this.allowedPlayerNames
            .split(",")
            .indexOf(this.scores[i].PlayerName) >= 0
        ) {
          this.allPlayerNames.push(this.scores[i].PlayerName);
        }
        // Save a list of all the course names
        if (
          this.allCourseNames.indexOf(this.scores[i].CourseName) < 0 &&
          holes.length == this.numberOfHoles
        ) {
          this.allCourseNames.push(this.scores[i].CourseName);
        }
        var allowFilteredPlayers =
          this.scores[i].PlayerName != "Par" &&
          (this.playerName == "All" ||
            this.scores[i].PlayerName == this.playerName);
        var allowFiltedHoles =
          this.numberOfHoles == 0 || this.numberOfHoles == holes.length;
        var allowFilteredCourses =
          this.courseName == "All" ||
          this.courseName == this.scores[i].CourseName;
        if (
          allowFilteredPlayers &&
          holes.length &&
          allowFiltedHoles &&
          allowFilteredCourses
        ) {
          filtered.push({
            PlayerName: this.scores[i].PlayerName,
            CourseName: this.scores[i].CourseName,
            LayoutName: this.scores[i].LayoutName,
            Date: new Date(this.scores[i].Date),
            TotalScore: this.scores[i].Total,
            OverUnderPar: this.scores[i]["+/-"],
            Holes: holes
          });
        }
      }
      // Sort results
      if (this.getSortPar != "") {
        var direction = this.getSortPar;
        filtered = filtered.sort(function(a, b) {
          return direction == "desc" ? b.OverUnderPar - a.OverUnderPar : a.OverUnderPar - b.OverUnderPar;
        });
      }
      if (this.getSortDate != "") {
        var direction = this.getSortDate;
        filtered = filtered.sort(function(a, b) {
          return direction == "desc" ? b.Date - a.Date : a.Date - b.Date;
        });
      }
      return filtered;
    }
  }
};
</script>

<style scoped>
select {
  width: 200px;
}
.header {
  font-weight: bold;
}
.header > div:hover {
  background-color: #77ed8c;
}
.player,
.par,
.course,
.hole,
.date {
  display: inline-block;
  height: 25px;
  line-height: 25px;
}
.player {
  width: 100px;
  text-align: left;
}
.course {
  width: 200px;
  text-align: left;
}
.par {
  width: 50px;
  text-align: right;
}
.date {
  width: 100px;
  text-align: right;
}
.hole {
  display: inline-block;
  width: 25px;
  text-align: center;
}
.par-3 {
  background-color: #2ca0b0;
}
.par-2 {
  background-color: #73d4e1;
}
.par-1 {
  background-color: #77ed8c;
}
.par0 {
  background-color: #cccccc;
}
.par1 {
  background-color: #ff8980;
}
.par2 {
  background-color: #ff695d;
}
.par3 {
  background-color: #ff4b3d;
}
.par4 {
  background-color: #ff2210;
}
.par5 {
  background-color: #cc0f00;
}
.par6 {
  background-color: #a60c00;
  color: #ffffff;
}
.par7 {
  background-color: #7c0900;
  color: #ffffff;
}
.par8 {
  background-color: #560600;
  color: #ffffff;
}
.par9 {
  background-color: #2a0300;
  color: #ffffff;
}
</style>
