<script setup>
import { BIconXCircle } from "bootstrap-icons-vue";
</script>
<script>
export default {
  props: {
    auth: Object,
    username: String,
    settings: Object,
    showSettings: Boolean,
    loggedIn: Boolean,
  },
  data() {
    return {
      settingChange: false,
      deletePopUp: false,
      time: {
        focus: {
          hours: Math.floor(this.settings.focus / 3600),
          minutes: Math.floor((this.settings.focus % 3600) / 60),
          seconds: Math.floor(this.settings.focus % 60),
        },

        shortRest: {
          hours: Math.floor(this.settings.shortRest / 3600),
          minutes: Math.floor((this.settings.shortRest % 3600) / 60),
          seconds: Math.floor(this.settings.shortRest % 60),
        },
        longRest: {
          hours: Math.floor(this.settings.longRest / 3600),
          minutes: Math.floor((this.settings.longRest % 3600) / 60),
          seconds: Math.floor(this.settings.longRest % 60),
        },
        focusTilLongRest: this.settings.focusTilLongRest,
      },
    };
  },
  methods: {
    toggleSettings() {
      this.$emit("toggleSettings");
    },
    toggleDeletePopup() {
      this.$emit("toggleDeleteAccountOverview");
      this.deletePopUp = !this.deletePopUp;
    },

    handleSignOut() {
      this.$emit("logout");
    },

    convertTimeFormatToSeconds(time) {
      return time.hours * 3600 + time.minutes * 60 + time.seconds;
    },
    async handleSaveChanges() {
      if (!this.settingChange) {
        return;
      }

      const updatedSettings = {
        focus: this.convertTimeFormatToSeconds(this.time.focus),
        focusTilLongRest: this.time.focusTilLongRest * 1,
        longRest: this.convertTimeFormatToSeconds(this.time.longRest),
        shortRest: this.convertTimeFormatToSeconds(this.time.shortRest),
      };
      this.$emit("updateSettings", updatedSettings);
    },
  },
  computed: {},
  watch: {
    time: {
      deep: true,
      handler(newTime) {
        let newFocus = this.convertTimeFormatToSeconds(newTime.focus);
        let newShortRest = this.convertTimeFormatToSeconds(newTime.shortRest);
        let newLongRest = this.convertTimeFormatToSeconds(newTime.longRest);
        if (
          newFocus != this.settings.focus ||
          newShortRest != this.settings.shortRest ||
          newLongRest != this.settings.longRest ||
          newTime.focusTilLongRest != this.settings.focusTilLongRest
        ) {
          this.settingChange = true;
        } else {
          this.settingChange = false;
        }
      },
    },
  },
  created() {},
  mounted() {},
};
</script>
<template>
  <div class="container" v-if="showSettings">
    <div class="innerContainer">
      <div class="header">
        <h1>SETTINGS</h1>
        <BIconXCircle @click="toggleSettings" />
      </div>
      <h3 class="username">Account: {{ username ? username : "Guest" }}</h3>
      <div class="timerSettings">
        <h3>Timer Settings</h3>

        <div class="timeSettingContainer">
          <h4>Focus:</h4>
          <input id="focusHours" type="number" v-model="time.focus.hours" />
          <label for="focusHours">h</label><span>:</span>
          <input id="focusMinutes" type="number" v-model="time.focus.minutes" />
          <label for="focusMinutes">m</label><span>:</span>
          <input id="focusSeconds" type="number" v-model="time.focus.seconds" />
          <label for="focusSeconds">s</label>
        </div>
        <div class="timeSettingContainer">
          <h4>Short rest:</h4>
          <input id="focusHours" type="number" v-model="time.shortRest.hours" />
          <label for="focusHours">h</label><span>:</span>
          <input
            id="focusMinutes"
            type="number"
            v-model="time.shortRest.minutes"
          />
          <label for="focusMinutes">m</label><span>:</span>
          <input
            id="focusSeconds"
            type="number"
            v-model="time.shortRest.seconds"
          />
          <label for="focusSeconds">s</label>
        </div>
        <div class="timeSettingContainer">
          <h4>Long rest:</h4>
          <input id="focusHours" type="number" v-model="time.longRest.hours" />
          <label for="focusHours">h</label><span>:</span>
          <input
            id="focusMinutes"
            type="number"
            v-model="time.longRest.minutes"
          />
          <label for="focusMinutes">m</label><span>:</span>
          <input
            id="focusSeconds"
            type="number"
            v-model="time.longRest.seconds"
          />
          <label for="focusSeconds">s</label>
        </div>
        <div class="focusTilLongRestContainer">
          <label for="focusTilLongRest">Focus period's until long rest:</label>
          <input
            type="text"
            id="focusTilLongRest"
            v-model="time.focusTilLongRest"
          />
        </div>
      </div>
      <div class="audio">
        <h3>Audio</h3>
        <input type="checkbox" id="switch" class="checkbox" />
        <label for="switch" class="toggle">
          <p></p>
        </label>
      </div>
      <div class="saveContainer">
        <p
          class="warning"
          :style="!settingChange && { color: 'var(--background)' }"
        >
          Warning: Updating timer settings will cause the timer to reset
        </p>
        <button
          :class="settingChange ? 'saveBtn' : 'signOutBtn disabled'"
          class="btn"
          @click="handleSaveChanges"
        >
          SAVE CHANGES
        </button>
      </div>

      <div class="signOutBtnContainer" v-if="loggedIn">
        <button class="signOutBtn btn" @click="handleSignOut">SIGN OUT</button>
        <button class="deleteBtn btn" @click="toggleDeletePopup">
          DELETE ACCOUNT
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  position: absolute;
  z-index: 3;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--background);
  border: 4px solid var(--backgroundBorder);
  border-radius: 40px;
  padding: 24px;
  max-width: 440px;
}
.innerContainer {
  width: 100%;
}

.header {
  margin-bottom: 32px;
}
.header h1 {
  text-align: center;
  font-size: 24px;
  color: var(--textPrimary);
}

.header svg {
  position: absolute;
  right: 40px;
  top: 28px;
  cursor: pointer;
  font-size: 24px;
  font-weight: 500;
  color: var(--textPrimary);
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
.username {
  margin-bottom: 24px;
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
  margin: 24px 0 16px 0;
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
  cursor: pointer;
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
}
.btn {
  cursor: pointer;
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
  transition: 0.3s;
}
.signOutBtnContainer {
  display: flex;
  gap: 8px;
}

.saveContainer {
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin-bottom: 8px;
}

.signOutBtn {
  width: 100%;
  background-color: var(--grey);
}
.deleteBtn {
  width: 100%;
  background-color: var(--red);
}
.saveBtn {
  width: 100%;
  background-color: var(--green);
}
.disabled {
  cursor: default;
}
.deleteBtn:hover {
  background-color: #e64343;
}
.signOutBtn:hover {
  background-color: #929292;
}
.saveBtn:hover {
  background-color: #63b38c;
}
.warning {
  color: var(--red);
  font-weight: 600;
  text-align: center;
  font-size: 12px;
}
</style>
