<template>
  <div>
    <v-container fill-height fill-width fluid>
      <v-row align="center" justify="center">
        <v-menu
          v-model="newGame"
          :close-on-content-click="false"
          :nudge-width="300"
          offset-y
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn class="ma-4" dense fab dark v-bind="attrs" v-on="on"
              ><v-icon>mdi-restart</v-icon></v-btn
            >
          </template>
          <v-card color="#FEF9E7">
            <v-card-title primary-title> NEW GAME </v-card-title>
            <v-container class="grey lighten-5">
              <v-row no-gutters>
                <template v-for="(gameMode, index) in gameModes">
                  <v-col :key="gameMode">
                    <v-card
                      @click="startNewGame(gameMode)"
                      class="pa-2"
                      outlined
                      tile
                      ><span class="text-caption">[{{ index + 1 }}]</span>
                      {{ gameMode }}
                    </v-card>
                  </v-col>
                </template>
              </v-row>
            </v-container>
          </v-card>
        </v-menu>
      </v-row>
      <v-row align="center" justify="center">
        <v-menu
          v-model="menuA"
          :close-on-content-click="false"
          :nudge-width="200"
          offset-y
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              width="37.5%"
              rounded
              class="white--text"
              color="#1C2833"
              v-bind="attrs"
              v-on="on"
            >
              {{ scores.scoreA.name }}
            </v-btn>
          </template>
          <v-card>
            <v-list>
              <v-list-item>
                <v-list-item-content>
                  <v-text-field
                    v-model="scores.scoreA.name"
                    v-on:keyup.enter="saveNames()"
                    label="Name"
                    outlined
                    autofocus
                  ></v-text-field>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-card>
        </v-menu>
        <v-card
          elevation="5"
          tile
          color="#f7ffe6"
          width="25%"
          class="number-display"
        >
          <h1 class="text-right">{{ total }}</h1>
        </v-card>
        <v-menu
          v-model="menuB"
          :close-on-content-click="false"
          :nudge-width="200"
          offset-y
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              width="37.5%"
              rounded
              class="white--text"
              color="#1C2833"
              v-bind="attrs"
              v-on="on"
            >
              {{ scores.scoreB.name }}
            </v-btn>
          </template>
          <v-card>
            <v-list>
              <v-list-item>
                <v-list-item-content>
                  <v-text-field
                    v-model="scores.scoreB.name"
                    v-on:keyup.enter="saveNames()"
                    label="Name"
                    outlined
                    autofocus
                  ></v-text-field>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-card>
        </v-menu>
      </v-row>
      <v-row align="center" justify="center">
        <v-card
          height="300px"
          :color="scores.scoreA.color"
          width="37.5%"
          class="score-display"
        >
          <v-container fill-height align-content-center justify-center>
            <h3 class="text-center">{{ scores.scoreA.score }}</h3>
          </v-container>
        </v-card>
        <v-card height="300px" width="25%">
          <div class="vuertual-numeric-keyboard bg-light rounded border p-3">
            <v-btn
              primary
              height="100%"
              color="#1C2833"
              class="white--text"
              elevation="2"
              v-for="key in keys"
              :key="key"
              v-text="key"
              @click="thisTotal(key)"
              :ripple="false"
            >
            </v-btn>
            <v-btn
              primary
              height="100%"
              color="#1C2833"
              class="white--text"
              elevation="2"
              @dblclick="undo()"
              ><v-icon dark> mdi-undo-variant </v-icon>
            </v-btn>
            <v-btn
              primary
              height="100%"
              color="#1C2833"
              class="white--text"
              elevation="2"
              @click="thisTotal(0)"
              :ripple="false"
              >0
            </v-btn>
            <v-btn
              primary
              height="100%"
              color="#1C2833"
              class="white--text"
              elevation="2"
              @click="backspace()"
            >
              <v-icon dark> mdi-backspace </v-icon>
            </v-btn>
          </div>
          <v-btn
            width="100%"
            height="55px"
            class="white--text mt-1"
            @click="enter()"
            color="#001a33"
            >ENTER</v-btn
          >
        </v-card>
        <v-card
          height="300px"
          :color="scores.scoreB.color"
          width="37.5%"
          class="score-display"
          ><v-container fill-height align-content-center justify-center>
            <h3 class="text-center">{{ scores.scoreB.score }}</h3>
          </v-container>
        </v-card>
      </v-row>
    </v-container>
    <v-dialog
      v-model="winnerDialog"
      transition="dialog-bottom-transition"
      max-width="600"
    >
      <v-card>
        <v-toolbar color="success" dark>WE HAVE A WINNER</v-toolbar>
        <v-card-text align-center>
          <div class="text-h2 pa-12">{{ winner }} WINS!</div>
        </v-card-text>
        <v-card-actions class="justify-end">
          <v-btn text @click="winnerDialog = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>


<script>
export default {
  name: "Calculator",
  data() {
    return {
      keys: [1, 2, 3, 4, 5, 6, 7, 8, 9],
      gameModes: [301, 501, 701, 1001],
      total: 0,
      winner: "Nobody",
      menuA: false,
      menuB: false,
      newGame: false,
      winnerDialog: false,
      scores: {
        scoreA: {
          name: "Player A",
          score: 501,
          color: "#EDF96E",
          active: true,
        },
        scoreB: { name: "Player B", score: 501, color: "white", active: false },
      },
      lastScores: {},
    };
  },
  props: {
    msg: String,
  },
  methods: {
    thisTotal(key) {
      if (this.total * 10 + key <= 180) {
        this.total = this.total * 10 + key;
      }
    },
    backspace() {
      this.total = (this.total - (this.total % 10)) / 10;
    },
    switchTurn() {
      this.scores.scoreA.active = !this.scores.scoreA.active;
      this.scores.scoreB.active = !this.scores.scoreB.active;
    },
    saveNames() {
      this.menuA = false;
      this.menuB = false;
      localStorage.setItem("playerA", this.scores.scoreA.name);
      localStorage.setItem("playerB", this.scores.scoreB.name);
    },
    enter() {
      this.lastScores = JSON.parse(JSON.stringify(this.scores));
      if (this.scores.scoreA.active) {
        if (this.scores.scoreA.score - this.total < 0) {
          this.total = 0;
        } else {
          this.scores.scoreA.score = this.scores.scoreA.score - this.total;
          this.total = 0;
          this.switchTurn();
          this.scores.scoreA.color = "white";
          this.scores.scoreB.color = "#EDF96E";
        }
      } else {
        if (this.scores.scoreB.score - this.total < 0) {
          this.total = 0;
        } else {
          this.scores.scoreB.score = this.scores.scoreB.score - this.total;
          this.total = 0;
          this.switchTurn();
          this.scores.scoreB.color = "white";
          this.scores.scoreA.color = "#EDF96E";
        }
      }
      if (this.scores.scoreB.score == 0) {
        this.winner = this.scores.scoreB.name;
        this.winnerDialog = true;
      }
      if (this.scores.scoreA.score == 0) {
        this.winner = this.scores.scoreA.name;
        this.winnerDialog = true;
      }
    },
    undo() {
      if (Object.entries(this.lastScores).length !== 0) {
        const ScoreHolder = this.scores;
        const lastScoreHolder = this.lastScores;
        this.scores = lastScoreHolder;
        this.lastScores = ScoreHolder;
        this.total = 0;
      }
    },
    startNewGame(gameMode) {
      this.scores.scoreA.score = gameMode;
      this.scores.scoreB.score = gameMode;
      this.total = 0;
      this.newGame = false;
    },
  },
  created: function () {
    console.log("test 999");
    if (localStorage.getItem("playerA") !== null) {
      // get stored name
      this.scores.scoreA.name = localStorage.getItem("playerA");
    }
    if (localStorage.getItem("playerB") !== null) {
      // get stored name
      this.scores.scoreB.name = localStorage.getItem("playerB");
    }
    window.addEventListener("keyup", (e) => {
      3;
      if (e.key == "Enter") {
        this.enter();
      } else if (e.key == "Backspace") {
        this.backspace();
        3333333;
      } else if (e.key == "-") {
        this.undo();
      } else if (e.key == "+") {
        this.newGame = true;
      } else if (this.newGame == true) {
        if (e.key == "1") {
          this.startNewGame(301);
        }
        if (e.key == "2") {
          this.startNewGame(501);
        }
        if (e.key == "3") {
          this.startNewGame(701);
        }
        if (e.key == "4") {
          this.startNewGame(1001);
        }
      } else if (this.winnerDialog == true) {
        this.winnerDialog = false;
        this.newGame = true;
      } else {
        const num = e.key.replace(/[^0-9]/g, "");
        if (num) {
          this.thisTotal(Number(num));
        }
      }
    });
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Orbitron&display=swap");

.vuertual-numeric-keyboard {
  display: grid;
  grid-template-columns: auto auto auto;
  height: 80%;
  grid-gap: 3px;
}
.vuertual-numeric-keyboard .btn {
  font-weight: bold;
}
.number-display {
  font-family: "Orbitron", sans-serif;
}
.score-display {
  font-family: "Orbitron", sans-serif;
  font-size: 72px;
}
</style>