<script setup>
import PopupCard from "../components/UI/PopupCard.vue";
import { BIconXLg } from "bootstrap-icons-vue";
</script>
<template>
  <PopupCard>
    <div class="header">
      <h2>Account name: {{ username }}</h2>
      <h1>SETTINGS</h1>
      <BIconXLg />
    </div>

    <div class="timerSettings">
      <h3>Timer Settings:</h3>
      <div>
        <label for="focus">Focus:</label>
        <input
          type="text"
          id="focus"
          v-model="focus"
          @input="enterTime"
          ref="focusInput"
        />
      </div>
      <div>
        <label for="shortRest">Short rest:</label>
        <input type="text" id="shortRest" v-model="shortRest" />
      </div>
      <div>
        <label for="longRest">Long Rest:</label>
        <input type="text" id="longRest" v-model="longRest" />
      </div>
      <div>
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
  methods: {
    enterTime() {
      let caretPosition = this.$refs.focusInput.selectionStart;
      //doing the minut
      this.focus =
        this.focus.slice(0, caretPosition) +
        this.focus.slice(caretPosition + 1);
      console.log(caretPosition);

      this.$nextTick(() => {
        this.$refs.focusInput.focus();
        this.$refs.focusInput.setSelectionRange(caretPosition, caretPosition);
      });
      // var regex = /^[0-9:]*$/;
      // if (!regex.test(this.focus)) {
      //   // If the input does not match the pattern, clear it
      //   this.focus = this.focus.slice(0, -1);
      // }
    },
  },
  mounted() {
    let totalSeconds = this.settings.focus;
    let hours = Math.floor(totalSeconds / 3600);
    totalSeconds %= 3600;
    let minutes = Math.floor(totalSeconds / 60);
    let seconds = totalSeconds % 60;

    let hoursStr = String(hours).padStart(2, "0");
    let minutesStr = String(minutes).padStart(2, "0");
    let secondsStr = String(seconds).padStart(2, "0");

    let timeStr = `${hoursStr}h:${minutesStr}m:${secondsStr}s`;

    this.focus = timeStr;
  },
};
</script>
<style scoped>
.header {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.header h1 {
  position: absolute;
  font-size: 16px;

  left: 50%;
  transform: translateX(-50%);
}
.header h1,
.header h2,
.header svg {
  font-size: 24px;
  font-weight: 400;
  color: var(--textPrimary);
}
</style>
<!-- this.$nextTick(() => {
  let caretPosition = 2; // Change this to the desired caret position
  this.$refs.focusInput.focus();
  this.$refs.focusInput.setSelectionRange(caretPosition, caretPosition);
}); -->
