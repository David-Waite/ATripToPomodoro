<script setup>
import { deleteUser, signOut } from "firebase/auth";
import PopupCard from "../components/UI/PopupCard.vue";
import { BIconXCircle } from "bootstrap-icons-vue";
import { deleteDoc, doc, getFirestore, updateDoc } from "firebase/firestore";
import db from "@/main";
</script>
<script>
export default {
  props: {
    auth: Object,
    username: String,
    settings: Object,
    showSettings: Boolean,
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
      this.deletePopUp = !this.deletePopUp;
    },

    handleSignOut() {
      signOut(this.auth);
    },

    async handleDelete() {
      const db = getFirestore();
      const user = this.auth.currentUser;
      const uid = user.uid;

      // Get a reference to the user's document in Firestore
      const userDoc = doc(db, "users", uid);

      try {
        // Delete the user's document from Firestore
        await deleteDoc(userDoc);
        console.log("User document deleted successfully");

        // Then delete the user's account
        await deleteUser(user);
        console.log("User account deleted successfully");
      } catch (error) {
        console.error("Error while deleting user account or document", error);
      }
    },
    convertTimeFormatToSeconds(time) {
      return time.hours * 3600 + time.minutes * 60 + time.seconds;
    },
    async handleSaveChanges() {
      if (!this.settingChange) {
        return;
      }
      const usersDoc = doc(db, "users", this.auth.currentUser.uid);
      await updateDoc(usersDoc, {
        settings: {
          focus: this.convertTimeFormatToSeconds(this.time.focus),
          focusTilLongRest: this.time.focusTilLongRest,
          longRest: this.convertTimeFormatToSeconds(this.time.longRest),
          shortRest: this.convertTimeFormatToSeconds(this.time.shortRest),
        },
      });
      this.$emit("refresh");
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
  <PopupCard v-if="showSettings">
    <div class="deleteDimmer" v-if="deletePopUp"></div>
    <div class="deletePopup" v-if="deletePopUp">
      <h2 class="deleteText">
        Are you sure you want to permanently delete your account?
      </h2>
      <div>
        <button class="deleteBtn btn" @click="handleDelete">
          DELETE ACCOUNT
        </button>
        <button class="signOutBtn btn" @click="toggleDeletePopup">
          CANCEL
        </button>
      </div>
    </div>
    <div class="header">
      <h2>Username: {{ username }}</h2>
      <h1>SETTINGS</h1>
      <BIconXCircle @click="toggleSettings" />
    </div>

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

    <div class="settingsFooter">
      <div class="signOutBtnContainer">
        <button class="signOutBtn btn" @click="handleSignOut">SIGN OUT</button>
        <button class="deleteBtn btn" @click="toggleDeletePopup">
          DELETE ACCOUNT
        </button>
      </div>
      <div class="saveContainer">
        <p class="warning" v-if="settingChange">
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
    </div>
  </PopupCard>
</template>

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
.header svg {
  cursor: pointer;
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
  margin-top: auto;
  display: flex;
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
}
.deletePopup {
  position: absolute;
  z-index: 7;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--background);
  border: 4px solid var(--backgroundBorder);
  border-radius: 40px;
  padding: 40px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
}
.deletePopup div {
  display: flex;
  gap: 16px;
}
.deleteDimmer {
  position: absolute;
  z-index: 6;
  background-color: rgba(0, 0, 0, 0.518);
  border: 4px solid var(--backgroundBorder);
  width: 98vw;
  height: 95vh;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border-radius: 40px;
}
.deleteText {
  color: var(--black);
}
</style>
