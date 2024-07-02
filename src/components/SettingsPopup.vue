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
        <input id="focusHours" type="text" />
        <label for="focusHours">h</label><span>:</span>
        <input id="focusMinutes" type="text" />
        <label for="focusMinutes">m</label><span>:</span>
        <input id="focusSeconds" type="text" />
        <label for="focusSeconds">s</label>
      </div>
      <div class="timeSettingContainer">
        <h4>Short rest:</h4>
        <input id="focusHours" type="text" />
        <label for="focusHours">h</label><span>:</span>
        <input id="focusMinutes" type="text" />
        <label for="focusMinutes">m</label><span>:</span>
        <input id="focusSeconds" type="text" />
        <label for="focusSeconds">s</label>
      </div>
      <div class="timeSettingContainer">
        <h4>Long rest:</h4>
        <input id="focusHours" type="text" />
        <label for="focusHours">h</label><span>:</span>
        <input id="focusMinutes" type="text" />
        <label for="focusMinutes">m</label><span>:</span>
        <input id="focusSeconds" type="text" />
        <label for="focusSeconds">s</label>
      </div>
      <div class="focusTilLongRestContainer">
        <label for="focusTilLongRest">focus period's until long rest:</label>
        <input type="text" id="focusTilLongRest" v-model="focusTilLongRest" />
      </div>
    </div>
    <br /><br /><br />
    next thing to do is to make the time input more userfreindy also you havent
    hooked up functions yet
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
      focus: "",
      shortRest: this.settings.shortRest,
      longRest: this.settings.longRest,
      focusTilLongRest: this.settings.focusTilLongRest,
      timeInputPlaceHolder: "",
    };
  },
  methods: {},
  mounted() {},
};
</script>
<style scoped>
.header {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 40px;
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
</style>
<!-- this.$nextTick(() => {
  let caretPosition = 2; // Change this to the desired caret position
  this.$refs.focusInput.focus();
  this.$refs.focusInput.setSelectionRange(caretPosition, caretPosition);
}); -->
