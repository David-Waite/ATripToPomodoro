<script setup>
import PopupCard from "../components/UI/PopupCard.vue";
import { BIconXLg } from "bootstrap-icons-vue";
</script>
<template>
  <PopupCard>
    <div class="header">
      <h2>Username: {{ username }}</h2>
      <h1>SETTINGS</h1>
      <BIconXLg />
    </div>

    <div class="timerSettings">
      <h3>Timer Settings</h3>

      <div class="timeSettingContainer">
        <h4>Focus:</h4>
        <input id="focusHours" type="text" v-model="formattedFocus.hours" />
        <label for="focusHours">h</label><span>:</span>
        <input id="focusMinutes" type="text" v-model="formattedFocus.minutes" />
        <label for="focusMinutes">m</label><span>:</span>
        <input id="focusSeconds" type="text" v-model="formattedFocus.seconds" />
        <label for="focusSeconds">s</label>
      </div>
      <div class="timeSettingContainer">
        <h4>Short rest:</h4>
        <input id="focusHours" type="text" v-model="formattedShortRest.hours" />
        <label for="focusHours">h</label><span>:</span>
        <input
          id="focusMinutes"
          type="text"
          v-model="formattedShortRest.minutes"
        />
        <label for="focusMinutes">m</label><span>:</span>
        <input
          id="focusSeconds"
          type="text"
          v-model="formattedShortRest.seconds"
        />
        <label for="focusSeconds">s</label>
      </div>
      <div class="timeSettingContainer">
        <h4>Long rest:</h4>
        <input id="focusHours" type="text" v-model="formattedLongRest.hours" />
        <label for="focusHours">h</label><span>:</span>
        <input
          id="focusMinutes"
          type="text"
          v-model="formattedLongRest.minutes"
        />
        <label for="focusMinutes">m</label><span>:</span>
        <input
          id="focusSeconds"
          type="text"
          v-model="formattedLongRest.seconds"
        />
        <label for="focusSeconds">s</label>
      </div>
      <div class="focusTilLongRestContainer">
        <label for="focusTilLongRest">focus period's until long rest:</label>
        <input type="text" id="focusTilLongRest" v-model="focusTilLongRest" />
      </div>
    </div>
    <div class="audio">
      <h3>Audio</h3>
      <input type="checkbox" id="switch" class="checkbox" />
      <label for="switch" class="toggle">
        <p></p>
      </label>
    </div>

    <div class="settingsFooter">
      <div class="signOutBtnContainer">
        <button class="signOutBtn btn">SIGN OUT</button>
        <button class="deleteBtn btn">DELETE ACCOUNT</button>
      </div>
      <div class="saveContainer">
        <p class="warning">
          Warning: Updating timer settings will cause the timer to reset
        </p>
        <button class="saveBtn btn">SAVE CHANGES</button>
      </div>
    </div>
  </PopupCard>
</template>

<script>
export default {
  props: {
    username: String,
    settings: Object,
  },
  data() {
    return {
      focus: {
        hours: this.settings.focus.hours,
        minutes: this.settings.focus.minutes,
        seconds: this.settings.focus.seconds,
      },

      shortRest: {
        hours: this.settings.shortRest.hours,
        minutes: this.settings.shortRest.minutes,
        seconds: this.settings.shortRest.seconds,
      },
      longRest: {
        hours: this.settings.longRest.hours,
        minutes: this.settings.longRest.minutes,
        seconds: this.settings.longRest.seconds,
      },
      focusTilLongRest: this.settings.focusTilLongRest,
    };
  },
  methods: {
    convertSecondsToTimeFormat(totalSeconds) {
      const hours = Math.floor(totalSeconds / 3600);
      const minutes = Math.floor((totalSeconds % 3600) / 60);
      const seconds = totalSeconds % 60;
      return { hours, minutes, seconds };
    },
  },
  computed: {
    formattedFocus() {
      return {
        hours: this.focus.hours.toString().padStart(2, "0"),
        minutes: this.focus.minutes.toString().padStart(2, "0"),
        seconds: this.focus.seconds.toString().padStart(2, "0"),
      };
    },
    formattedShortRest() {
      return {
        hours: this.shortRest.hours.toString().padStart(2, "0"),
        minutes: this.shortRest.minutes.toString().padStart(2, "0"),
        seconds: this.shortRest.seconds.toString().padStart(2, "0"),
      };
    },
    formattedLongRest() {
      return {
        hours: this.longRest.hours.toString().padStart(2, "0"),
        minutes: this.longRest.minutes.toString().padStart(2, "0"),
        seconds: this.longRest.seconds.toString().padStart(2, "0"),
      };
    },
  },
  created() {
    this.focus = this.convertSecondsToTimeFormat(this.settings.focus);
    this.shortRest = this.convertSecondsToTimeFormat(this.settings.shortRest);
    this.longRest = this.convertSecondsToTimeFormat(this.settings.longRest);
  },
  mounted() {
    console.log(this.settings);
  },
};
</script>
<style scoped>
.header {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 60px;
}
.header h1 {
  position: absolute;

  left: 50%;
  transform: translateX(-50%);
}

.header h1,
.header h2,
.header svg {
  font-size: 24px;
  font-weight: 500;
  color: var(--textPrimary);
}

h3 {
  font-size: 20px;
  font-weight: 500;
  color: var(--textPrimary);
}

.timerSettings {
  width: 340px;
  display: flex;
  flex-direction: column;
  gap: 16px;
  font-weight: 400;
}
.timeSettingContainer {
  display: flex;
  align-items: center;
  font-weight: 500;
  font-size: 16px;
}

.timeSettingContainer h4 {
  font-weight: 500;
  color: var(--textPrimary);
  margin-right: auto;
  font-size: 16px;
}
.timeSettingContainer input {
  width: 30px;
  font-size: 16px;
  background-color: var(--primary);
  color: var(--background);
  border: none;
  border-radius: 6px 0px 0px 6px;
  font-weight: 500;
  font-family: RobotoMono;
  padding-right: 2px;
  text-align: end;
}
.timeSettingContainer input:focus {
  outline-style: none;
}

.timeSettingContainer label {
  background-color: var(--primary);
  border: none;
  border-radius: 0px 6px 6px 0px;
  color: var(--background);
  padding-right: 8px;
  margin-left: -1px;
}

.timeSettingContainer span {
  color: var(--primary);
  width: 16px;
  text-align: center;
}

.focusTilLongRestContainer label {
  font-weight: 500;
  color: var(--textPrimary);
  margin-right: auto;
  font-size: 16px;
}
.focusTilLongRestContainer {
  display: flex;
}
.focusTilLongRestContainer input {
  width: 30px;
  font-size: 16px;
  background-color: var(--primary);
  color: var(--background);
  border: none;
  border-radius: 6px;
  font-family: RobotoMono;
  font-weight: 500;

  text-align: center;
}
.audio {
  margin-top: 60px;
  display: flex;
  gap: 16px;
  align-items: center;
}
.toggle {
  position: relative;
  display: inline-block;
  width: 56px;
  height: 30px;
  background-color: var(--grey);
  border-radius: 30px;
}

.toggle::after {
  content: "";
  position: absolute;
  width: 26px;
  height: 26px;
  border-radius: 50%;
  background-color: var(--background);
  top: 2px;
  left: 2px;
  transition: all 0.3s;
}

.toggle p {
  font-family: Arial, Helvetica, sans-serif;
  font-weight: bold;
}

.checkbox:checked + .toggle::after {
  left: 28px;
}

.checkbox:checked + .toggle {
  background-color: var(--primary);
}

.checkbox {
  display: none;
}

.settingsFooter {
  position: relative;
  margin-top: auto;
  display: flex;
}
.btn {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 180px;
  border-radius: 12px;
  height: 36px;
  font-size: 16px;
  font-weight: 600;
  color: var(--white);
  border: none;
}
.signOutBtnContainer {
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.saveContainer {
  align-self: flex-end;
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}
.signOutBtn {
  background-color: var(--grey);
}
.deleteBtn {
  background-color: var(--red);
}
.saveBtn {
  background-color: var(--green);
}
.warning {
  color: var(--red);
  font-weight: 600;
  text-align: center;
}
</style>
<!-- this.$nextTick(() => {
  let caretPosition = 2; // Change this to the desired caret position
  this.$refs.focusInput.focus();
  this.$refs.focusInput.setSelectionRange(caretPosition, caretPosition);
}); -->
