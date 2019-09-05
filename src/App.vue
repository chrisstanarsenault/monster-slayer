<template>
  <div id="app">
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">YOU</h1>
        <div class="healthbar">
          <div class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            :style='{width: playerHealth + "%"}'>
             {{ playerHealth }}
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">MONSTER</h1>
        <div class="healthbar">
          <div class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            :style='{width: monsterHealth + "%"}'>
            {{ monsterHealth }}
          </div>
        </div>
      </div>
    </section>
    <section class="row controls" v-if="!newGameStart">
      <div class="small-12 columns">
        <button id="start-game" @click="newGame()">START NEW GAME</button>
      </div>
    </section>
    <section class="row controls" v-else>
      <div class="small-12 columns">
        <button id="attack" @click="attack()">ATTACK</button>
        <button id="special-attack" @click="specialAttack()">SPECIAL ATTACK</button>
        <button id="heal" @click="heal()">HEAL</button>
        <button id="give-up" @click="giveUp()">GIVE UP</button>
      </div>
    </section>
    <section class="row log" v-if="newGameStart">
      <div class="small-12 columns">
        <ul>
          <transition-group name="fade" mode="out-in">
            <li
              v-for="log in logs"
              :class="{'player-turn': log.isPlayer, 'monster-turn':!log.isPlayer}"
              :key="log">
              {{ log.text }}
            </li>
          </transition-group>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>

export default {
  name: 'app',
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      newGameStart: false,
      winner: "",
      logs: [],
    };
  },
  methods: {
    newGame() {
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.newGameStart = !this.newGameStart;
      this.logs = [];
    },
    attack() {
      let playerAttack = Math.floor(Math.random() * 12);
      let monsterAttack = Math.floor(Math.random() * 12);
      this.playerHealth -= monsterAttack;
      this.monsterHealth -= playerAttack;
      this.logs.unshift({
        isPlayer: true,
        text: "Player attacked for: " + playerAttack
      });
      this.logs.unshift({
        isPlayer: false,
        text: "Monster attacked for: " + monsterAttack
      });

      this.healthCheck();

    },
    specialAttack() {
      let playerSpecialAttack = Math.floor(Math.random() * 20);
      let monsterAttack = Math.floor(Math.random() * 12);
      this.playerHealth -= monsterAttack;
      this.monsterHealth -= playerSpecialAttack;
      this.logs.unshift({
        isPlayer: true,
        text: "Player did a special attack for: " + playerSpecialAttack
      });
      this.logs.unshift({
        isPlayer: false,
        text: "Monster attacked for: " + monsterAttack
      });

      this.healthCheck();
    },
    heal() {
      let playerHeal = Math.floor(Math.random() * 20);
      let monsterAttack = Math.floor(Math.random() * 12);
      this.playerHealth += playerHeal;

      if (this.playerHealth >= 100) {
        this.playerHealth = 100;
        this.logs.unshift({
        isPlayer: true,
        text: "Player can not heal, already at max health!"
      });
      } else {
        this.logs.unshift({
        isPlayer: true,
        text: "Player healed for: " + playerHeal
      });
      }

      this.playerHealth -= monsterAttack;
      this.logs.unshift({
        isPlayer: false,
        text: "Monster attacked for: " + monsterAttack
      });

      this.healthCheck();
    },
    giveUp() {
      alert("Coward!!  You ran away.");
      this.newGameStart = false;
    },
    healthCheck() {
      if (this.playerHealth <= 0) {
        alert("Monster has killed you!  Resurrect and try again!");
        this.logs.unshift({
        isPlayer: true,
        text: "The monster has killed you!"
      });
        this.newGameStart = false;
      } else if (this.monsterHealth <= 0) {
        alert("You killed the monster!  Fight another one!");
        this.logs.unshift({
        isPlayer: true,
        text: "You have killed the monster!"
      });
        this.newGameStart = false;
      }
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.text-center {
    text-align: center;
}

.healthbar {
    width: 80%;
    height: 40px;
    background-color: #eee;
    margin: auto;
    transition: width 500ms;
}

.controls, .log {
    margin-top: 30px;
    text-align: center;
    padding: 10px;
    border: 1px solid #ccc;
    box-shadow: 0px 3px 6px #ccc;
}

.turn {
    margin-top: 20px;
    margin-bottom: 20px;
    font-weight: bold;
    font-size: 22px;
}

.log ul {
    list-style: none;
    font-weight: bold;
    text-transform: uppercase;
}

.log ul li {
    margin: 5px;
}

.log ul .player-turn {
    color: blue;
    background-color: #e4e8ff;
}

.log ul .monster-turn {
    color: red;
    background-color: #ffc0c1;
}

button {
    font-size: 20px;
    background-color: #eee;
    padding: 12px;
    box-shadow: 0 1px 1px black;
    margin: 10px;
}

#start-game {
    background-color: #aaffb0;
}

#start-game:hover {
    background-color: #76ff7e;
}

#attack {
    background-color: #ff7367;
}

#attack:hover {
    background-color: #ff3f43;
}

#special-attack {
    background-color: #ffaf4f;
}

#special-attack:hover {
    background-color: #ff9a2b;
}

#heal {
    background-color: #aaffb0;
}

#heal:hover {
    background-color: #76ff7e;
}

#give-up {
    background-color: #ffffff;
}

#give-up:hover {
    background-color: #c7c7c7;
}

.fade-enter {
  opacity: 0;
}

.fade-enter-active {
  transition: opacity 0.5s;
}
</style>
