<script setup>
import { BIconPause, BIconPlay, BIconStop } from "bootstrap-icons-vue";
</script>
<template>
  <div>
    <div class="base-timer">
      <div class="buttonContiainer">
        <button v-if="stage == 'start'" @click="startTimer">
          <BIconPlay />
        </button>
        <button v-if="stage == 'running'" @click="pauseTimer">
          <BIconPause />
        </button>
        <div class="pauseStopContainer">
          <button v-if="stage == 'paused'" @click="unpauseTimer">
            <BIconPlay />
          </button>
          <button v-if="stage == 'paused'" @click="stopTimer">
            <BIconStop />
          </button>
        </div>
      </div>
      <svg
        class="base-timer__svg"
        viewBox="0 0 100 100"
        xmlns="http://www.w3.org/2000/svg"
      >
        <g class="base-timer__circle">
          <circle
            class="base-timer__path-elapsed"
            cx="50"
            cy="50"
            r="45"
          ></circle>
          <path
            id="base-timer-path-remaining"
            :stroke-dasharray="strokeDashArray"
            class="base-timer__path-remaining"
            d="
            M 50, 50
            m -45, 0
            a 45,45 0 1,0 90,0
            a 45,45 0 1,0 -90,0
          "
          ></path>
        </g>
      </svg>

      <span id="base-timer-label" class="base-timer__label">
        {{ Math.floor(time / 60) }}:{{
          time % 60 < 10 ? "0" + (time % 60) : time % 60
        }}</span
      >
    </div>
  </div>
</template>

<script>
export default {
  props: {
    settings: Object,
    stage: String,
  },
  data() {
    return {
      strokeDashArray: 283,
      startingTime: this.settings.pomodoroTime * 60,
      time: this.settings.pomodoroTime * 60,
      intervalId: null,
      shortRest: this.settings.shortRest * 60,
      longRest: this.settings.longRest * 60,
      pomodoroTillLongRest: this.settings.pomodoroTillLongRest,
      pomodoroTillLongRestSetting: this.settings.pomodoroTillLongRest,
      pomodoroTime: this.settings.pomodoroTime * 60,
      current: "pomodoro",
      currentStage: this.stage,
    };
  },
  computed: {},

  watch: {
    time: function (newTime) {
      let rawTimeFraction = newTime / this.startingTime;

      rawTimeFraction =
        rawTimeFraction - (1 / this.startingTime) * (1 - rawTimeFraction);

      this.strokeDashArray = `${(rawTimeFraction * 283).toFixed(0)} 283`;
    },
    stage(newStage) {
      this.currentStage = newStage;
    },
  },

  methods: {
    startTimer() {
      this.$emit("updateStage", "running");
      this.intervalId = setInterval(() => {
        if (this.time > 0) {
          this.time--;
        } else {
          this.hitZero();
        }
      }, 1000);
    },
    pauseTimer() {
      this.$emit("updateStage", "paused");

      clearInterval(this.intervalId);
      this.intervalId = null;
    },
    unpauseTimer() {
      this.$emit("updateStage", "running");

      if (!this.intervalId) {
        this.startTimer();
      }
    },
    stopTimer() {
      this.pauseTimer();
      this.$emit("updateStage", "start");

      this.pomodoroTillLongRest = this.pomodoroTillLongRestSetting;
      this.time = this.pomodoroTime;
      this.startingTime = this.pomodoroTime;
      this.current = "pomodoro";
    },
    async hitZero() {
      this.pauseTimer();
      this.$emit("updateStage", "start");

      if (this.current === "pomodoro") {
        this.$emit("updateTimeStudying");
        this.$emit("updateStage", "start");
        this.pomodoroTillLongRest--;

        if (this.pomodoroTillLongRest === 0) {
          this.time = this.longRest;
          this.startingTime = this.longRest;
          this.current = "longRest";
          return;
        }
        this.time = this.shortRest;
        this.startingTime = this.shortRest;
        this.current = "shortRest";
        return;
      }
      if (this.current === "shortRest") {
        this.time = this.pomodoroTime;
        this.startingTime = this.pomodoroTime;
        this.current = "pomodoro";
        return;
      }
      if (this.current === "longRest") {
        this.stopTimer();
      }
    },
  },
};
</script>

<style scoped>
.pauseStopContainer {
  display: flex;
  gap: 12px;
}
.buttonContiainer {
  position: absolute;
  z-index: 2;
  bottom: 20%;
  left: 50%;
  transform: translate(-50%, -50%);
}
button {
  background-color: transparent;
  border: 2px solid rgb(237, 237, 237);
  border-radius: 50%;
  padding: 3px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.buttonContiainer svg {
  font-size: 24px;
  font-weight: 900;
  color: rgb(237, 237, 237);
}
.base-timer {
  position: relative;
  width: 300px;
  height: 300px;
}

.base-timer__svg {
  transform: scaleX(-1);
}

.base-timer__circle {
  fill: none;
  stroke: none;
}

.base-timer__path-elapsed {
  stroke-width: 1px;
  stroke: rgb(237, 237, 237);
}

.base-timer__path-remaining {
  stroke-width: 1px;
  stroke: aqua;
  stroke-linecap: round;
  transform: rotate(90deg);
  transform-origin: center;
  transition: 1s linear all;
  fill-rule: nonzero;
  stroke: aqua;
}

.base-timer__label {
  position: absolute;
  width: 300px;
  height: 300px;
  top: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 48px;
  color: white;
}
</style>
