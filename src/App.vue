<template>
  <header>
    <h1>Monster Slayder</h1>
    <h2>
      Round: <span> {{ count }} </span>
    </h2>
  </header>
  <div id="game">
    <section id="monster" class="container">
      <h2>Monster Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="showHealthMonster">
          {{ monsterHealth }}
        </div>
      </div>
    </section>

    <section id="player" class="container">
      <h2>Your Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="showHealthPlayer">
          {{ playerHealth }}
        </div>
      </div>
    </section>

    <section id="controls" v-show="winner == null">
      <button @click="handleAttack">ATTACK</button>
      <button @click="handleSpecialAttack" :disabled="canUseSpecial">
        SPECIAL ATTACK
      </button>
      <button @click="handleHeal" :disabled="canUseHeal">HEAL</button>
      <button @click="handleSurrender" :disabled="canUseSurrender">
        SURRENDER
      </button>
    </section>

    <!-- modal -->
    <teleport to="body">
      <noti-toast @start="start" v-show="winner !== null" />
    </teleport>

    <section id="log" class="container">
      <h2>Battle Log</h2>
      <ul>
        <li v-for="(log, inx) in logMessages" :key="inx">
          <p v-if="log.actionBy == 'player'">Round: {{ log.round }}</p>
          <span
            :class="{
              'log--player': log.actionBy == 'player',
              'log--monster': log.actionBy == 'monster',
            }"
            >{{ log.actionBy }}
          </span>
          &nbsp
          <span
            :class="{
              'log--damage':
                log.actionType == 'attack' ||
                log.actionType == 'special attack',
              'log--heal': log.actionType == 'heal',
            }"
          >
            {{ log.actionType }}
          </span>
          <span> and value is </span>
          <span
            :class="{
              'log--damage':
                log.actionType == 'attack' ||
                log.actionType == 'special attack',
              'log--heal': log.actionType == 'heal',
            }"
          >
            {{ log.actionValue }}
          </span>
        </li>
      </ul>
    </section>
  </div>
</template>

<script>
import Notification from './components/Notification.vue';
function getRandomValue(min, max) {
  return Math.floor(Math.random() * (max - min) + min);
}
export default {
  name: 'App',
  components: {
    notiToast: Notification,
  },
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      winner: null,
      count: 1,
      logMessages: [],
    };
  },
  methods: {
    handleAttack() {
      let attackValue = getRandomValue(5, 10);
      this.monsterHealth -= attackValue;

      this.monsterAttack();
      this.saveLogs('player', 'attack', attackValue);
    },
    monsterAttack() {
      this.count += 1;
      let attackValue = getRandomValue(8, 15);
      this.playerHealth -= attackValue;
      this.saveLogs('monster', 'attack', attackValue);
    },
    handleSpecialAttack() {
      let attackValue = getRandomValue(10, 18);
      this.monsterHealth -= attackValue;
      this.monsterAttack();
      this.saveLogs('player', 'special attack', attackValue);
    },
    handleHeal() {
      let healValue = getRandomValue(20, 25);
      this.playerHealth += healValue;
      this.monsterAttack();
      this.saveLogs('player', 'heal', healValue);
    },
    handleSurrender() {
      this.playerHealth = 0;
    },
    start() {
      this.monsterHealth = 100;
      this.playerHealth = 100;
      this.count = 1;
      this.winner = null;
      this.logMessages = [];
    },
    saveLogs(who, type, value) {
      this.logMessages.unshift({
        actionBy: who,
        actionType: type,
        actionValue: value,
        round: this.count - 1,
      });
    },
  },
  computed: {
    showHealthPlayer() {
      return { width: this.playerHealth + '%' };
    },
    showHealthMonster() {
      return { width: this.monsterHealth + '%' };
    },
    canUseSpecial() {
      return this.count % 3 !== 0;
    },
    canUseHeal() {
      return this.count % 3 !== 0;
    },
    canUseSurrender() {
      return this.count == 1;
    },
  },
  watch: {
    playerHealth(value) {
      if (value <= 0 && this.monsterHealth <= 0) {
        this.winner = 'Draw';
        this.monsterHealth = 0;
        this.playerHealth = 0;
      } else if (value <= 0) {
        this.winner = 'Monster';
        value = 0;
        this.playerHealth = 0;
      }
    },
    monsterHealth(value) {
      if (value <= 0 && this.playerHealth <= 0) {
        this.winner = 'Draw';
        this.monsterHealth = 0;
        this.playerHealth = 0;
      } else if (value <= 0) {
        this.winner = 'Player';
        value = 0;
        this.monsterHealth = 0;
      }
    },
  },
};
</script>

<style>
* {
  box-sizing: border-box;
}

html {
  font-family: 'Jost', sans-serif;
}

body {
  margin: 0;
}

header {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 0.5rem;
  background-color: #880017;
  color: white;
  text-align: center;
  margin-bottom: 2rem;
}

section {
  width: 90%;
  max-width: 40rem;
  margin: auto;
}

.healthbar {
  width: 100%;
  height: 40px;
  border: 1px solid #575757;
  margin: 1rem 0;
  background: #fde5e5;
}

.healthbar__value {
  background-color: #00a876;
  width: 100%;
  height: 100%;
  color: #fff;
  line-height: 40px;
}

.container {
  text-align: center;
  padding: 0.5rem;
  margin: 1rem auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 12px;
}

#monster h2,
#player h2 {
  margin: 0.25rem;
}

#controls {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

button {
  font: inherit;
  border: 1px solid #88005b;
  background-color: #88005b;
  color: white;
  padding: 1rem 2rem;
  border-radius: 12px;
  margin: 1rem;
  width: 12rem;
  cursor: pointer;
  box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
}

button:focus {
  outline: none;
}

button:hover,
button:active {
  background-color: #af0a78;
  border-color: #af0a78;
  box-shadow: 1px 1px 8px rgba(0, 0, 0, 0.26);
}

button:disabled {
  background-color: #ccc;
  border-color: #ccc;
  box-shadow: none;
  color: #3f3f3f;
  cursor: not-allowed;
}

#log ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

#log li {
  margin: 0.5rem 0;
}

.log--player {
  color: #7700ff;
}

.log--monster {
  color: #da8d00;
}

.log--damage {
  color: red;
}

.log--heal {
  color: green;
}
</style>
