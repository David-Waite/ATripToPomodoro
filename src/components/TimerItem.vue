<script setup>
import { BIconPause, BIconPlay, BIconStop } from "bootstrap-icons-vue";
</script>
<template>
  <div>
    <div
      class="base-timer"
      :class="stage === 'running' ? 'running' : 'notRunning'"
    >
      <p class="currentTimerText">
        {{
          current === "pomodoro"
            ? "Focus"
            : current === "shortRest"
            ? "Short Rest"
            : "Long Rest"
        }}
      </p>
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
        {{
          Math.floor(time / 3600) > 0
            ? Math.floor(time / 3600)
                .toString()
                .padStart(2, "0") +
              ":" +
              Math.floor((time % 3600) / 60)
                .toString()
                .padStart(2, "0") +
              ":" +
              (time % 60).toString().padStart(2, "0")
            : Math.floor(time / 60) +
              ":" +
              (time % 60).toString().padStart(2, "0")
        }}
      </span>
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
      startingTime: this.settings.focus,
      time: this.settings.focus,
      intervalId: null,
      shortRest: this.settings.shortRest,
      longRest: this.settings.longRest,
      pomodoroTillLongRest: this.settings.focusTilLongRest,
      pomodoroTillLongRestSetting: this.settings.focusTilLongRest,
      focus: this.settings.focus,
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
      this.time = this.focus;
      this.startingTime = this.focus;
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
        this.time = this.focus;
        this.startingTime = this.focus;
        this.current = "pomodoro";
        return;
      }
      if (this.current === "longRest") {
        this.stopTimer();
      }
    },
  },
  mounted() {},
};
</script>

<style scoped>
.pauseStopContainer {
  display: flex;
  gap: 8px;
}
.running .buttonContiainer svg {
  color: transparent;
}

.running:hover .buttonContiainer svg {
  color: rgb(237, 237, 237);
}

.running button {
  border-color: transparent;
}
.running:hover button {
  border-color: rgb(237, 237, 237);
}
.notRunning .buttonContiainer svg {
  color: rgb(237, 237, 237);
}
.notRunning button {
  border-color: rgb(237, 237, 237);
}

.buttonContiainer {
  display: block;
  position: absolute;
  z-index: 2;
  transition: 0.3s;
  bottom: 13%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.currentTimerText {
  position: absolute;
  bottom: 27%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: var(--white);
  font-size: 20px;
  font-weight: 500;
}
button {
  background-color: transparent;
  border: 2px solid;
  border-radius: 50%;
  padding: 3px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: 0.3s;
}
.buttonContiainer svg {
  font-size: 20px;
  transition: 0.3s;
  font-weight: 900;

  cursor: pointer;
}
.base-timer {
  position: relative;
  width: 330px;
  height: 330px;
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
  stroke: #dfdfdf93;
}

.base-timer__path-remaining {
  stroke-width: 1px;

  stroke-linecap: round;
  transform: rotate(90deg);
  transform-origin: center;
  transition: 1s linear all;
  fill-rule: nonzero;
  stroke: var(--white);
  filter: drop-shadow(0 0 1px white);
}

.base-timer__label {
  position: absolute;
  width: 330px;
  height: 330px;
  top: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 64px;
  font-weight: 300;
  color: white;
}
</style>
