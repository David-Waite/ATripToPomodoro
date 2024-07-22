<script setup>
import {
  getAuth,
  onAuthStateChanged,
  signInWithEmailAndPassword,
  signOut,
} from "firebase/auth";
import LoopingBackground from "@/components/LoopingBackground.vue";
import { doc, getDoc, updateDoc } from "firebase/firestore";
import db from "@/main";

import TimerItem from "../components/TimerItem.vue";
import NavItem from "../components/NavItem.vue";
import SettingsPopup from "@/components/SettingsPopup.vue";
import ShopPopup from "@/components/ShopPopup.vue";
import PomodoroDollarsEarned from "@/components/UI/PomodoroDollarsEarned.vue";
import AccountPopup from "@/components/AccountPopup.vue";
import { nextTick } from "vue";
</script>

<script>
export default {
  data() {
    return {
      contentLoaded: false,
      guestUser: {
        timeStudying: 5000,
        username: null,
        vehiclesOwned: [
          {
            price: 0,
            status: "equipped",
            name: "Classic",
          },
        ],
        settings: {
          longRest: 1800,
          focusTilLongRest: 4,
          focus: 1500,
          shortRest: 300,
        },
        email: null,
      },
      auth: {},
      loggedIn: false,
      currentUserDocRef: null,
      userData: null,
      showSettings: false,
      showShop: false,
      stage: "start",
      showMoneyEarned: false,
      showAccountPopUp: false,
    };
  },
  methods: {
    toggleAccount() {
      this.showAccountPopUp = !this.showAccountPopUp;
    },
    toggleSettings() {
      this.showSettings = !this.showSettings;
    },
    toggleShop() {
      this.showShop = !this.showShop;
    },
    async login(email, password) {
      this.contentLoaded = false;
      try {
        await signInWithEmailAndPassword(getAuth(), email, password);
      } catch (error) {
        console.log(error.code);

        console.error("Error creating user:", error);
      }
    },
    async logout() {
      this.contentLoaded = false;
      await signOut(this.auth);
    },
    refresh() {
      this.stage = "start";
      this.showSettings = false;
      this.showShop = false;
      this.showAccountPopUp = false;
    },
    // getting current users data
    async fetchUser() {
      if (!this.loggedIn) {
        console.log("got here");
        this.userData.vehiclesOwned = await this.checkForVehiclesToAdd();
        this.contentLoaded = true;
        return;
      }

      let vehiclesToBeAdded = await this.checkForVehiclesToAdd();

      if (vehiclesToBeAdded.length > 0) {
        await updateDoc(this.currentUserDocRef, {
          vehiclesOwned: vehiclesToBeAdded,
        });
      }
      this.contentLoaded = true;
    },

    //Updates userfirestore with new vehicles
    async checkForVehiclesToAdd() {
      //getting and setting vehicles array
      const vehiclesSnapshot = await getDoc(
        doc(db, "vehicles", "FFtkXZWQgK9yTYWFTd7C")
      );
      let vehiclesData = vehiclesSnapshot.data();
      vehiclesData = vehiclesData["all"];
      let vechiclesToBeAdded = this.userData.vehiclesOwned;
      //checks to see if any new vehicles have been added to the firebase, and updates the users firestore
      if (this.userData.vehiclesOwned.length != vehiclesData.length) {
        for (let vehicle of vehiclesData) {
          let isVehicleOwned = this.userData.vehiclesOwned.some(
            (userVehicle) => userVehicle.name === vehicle.name
          );

          if (!isVehicleOwned) {
            vechiclesToBeAdded.push({
              name: vehicle.name,
              price: vehicle.price,
              status: "buy",
            });
          }
        }
      }
      return vechiclesToBeAdded;
    },

    //SETTER for stage
    updateStage(newStage) {
      this.stage = newStage;
    },

    //Increases timestudying (pomodoro dollars)
    async updateTimeStudying() {
      const timeStudying =
        Number(this.userData.timeStudying) +
        Number(this.userData.settings.focus);

      if (this.loggedIn) {
        await updateDoc(this.currentUserDocRef, { timeStudying: timeStudying });
      }
      this.userData.timeStudying = timeStudying;
      this.showMoneyEarned = true;
      setTimeout(() => {
        this.showMoneyEarned = false;
      }, 2000);
    },

    //Buy vehicle (updating firestore)
    async buyVehicle(vehicle) {
      if (this.userData.timeStudying >= vehicle.price) {
        const vehicleIndex = this.userData.vehiclesOwned.findIndex(
          (v) => v.name === vehicle.name && v.price === vehicle.price
        );
        if (vehicleIndex !== -1) {
          this.userData.vehiclesOwned[vehicleIndex].status = "equip";
          this.userData.timeStudying =
            this.userData.timeStudying - vehicle.price;
          if (!this.loggedIn) {
            return;
          }
          await updateDoc(this.currentUserDocRef, {
            vehiclesOwned: this.userData.vehiclesOwned,
            timeStudying: this.userData.timeStudying,
          });
        }
      }
    },
    //Equipping vehicle (firestore)
    async equipVehicle(vehicle) {
      const vehicleToUnequip = this.userData.vehiclesOwned.findIndex(
        (v) => v.status === "equipped"
      );

      const vehicleToEquip = this.userData.vehiclesOwned.findIndex(
        (v) => v.name === vehicle.name
      );

      this.userData.vehiclesOwned[vehicleToUnequip].status = "equip";
      this.userData.vehiclesOwned[vehicleToEquip].status = "equipped";

      if (!this.loggedIn) {
        return;
      }
      await updateDoc(this.currentUserDocRef, {
        vehiclesOwned: this.userData.vehiclesOwned,
      });
    },
    async updateSettings(settings) {
      console.log(settings);

      if (this.loggedIn) {
        await updateDoc(this.currentUserDocRef, {
          settings: settings,
        });
      }
      this.userData.settings = { ...settings };
      this.refresh();
      this.$forceUpdate();
    },
  },

  computed() {},
  watch: {
    userData: function (userData) {
      this.userData = userData;
    },
  },
  async mounted() {
    this.auth = getAuth();
    onAuthStateChanged(this.auth, async (user) => {
      this.refresh();

      this.userData = null;
      await nextTick();
      if (!user) {
        this.loggedIn = false;
        this.userData = this.guestUser;

        await this.fetchUser();
      } else {
        this.loggedIn = true;
        this.currentUserDocRef = doc(db, "users", this.auth.currentUser.uid);
        const docSnap = await getDoc(
          doc(db, "users", this.auth.currentUser.uid)
        );
        if (docSnap.exists()) {
          this.userData = docSnap.data();
        } else {
          console.log("No such document!");
        }
        await this.fetchUser();
      }
    });
  },
};
</script>

<template>
  <div class="container" v-if="contentLoaded">
    <PomodoroDollarsEarned
      :visable="showMoneyEarned"
      :earnedAmount="userData.settings.focus"
    />
    <AccountPopup
      :show="showAccountPopUp"
      @toggleAccount="toggleAccount"
      @refresh="refresh"
      @login="login"
    />
    <NavItem
      :loggedIn="loggedIn"
      @toggleSettings="toggleSettings"
      :username="userData.username"
      @toggleShop="toggleShop"
      @toggleAccount="toggleAccount"
    />
    <SettingsPopup
      :username="userData.username"
      :settings="userData.settings"
      :loggedIn="loggedIn"
      :showSettings
      :auth="auth"
      @toggleSettings="toggleSettings"
      @updateSettings="updateSettings"
      @refresh="refresh"
      @logout="logout"
    />
    <ShopPopup
      :showShop
      @toggleShop="toggleShop"
      :userData="userData"
      @equipVehicle="equipVehicle"
      @buyVehicle="buyVehicle"
    />

    <LoopingBackground :stage="stage" :vehicles="userData.vehiclesOwned" />
    <div class="timerContainer">
      <TimerItem
        :stage="stage"
        :settings="userData.settings"
        @updateTimeStudying="updateTimeStudying"
        @updateStage="updateStage"
      />
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 100vw;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
}
.timerContainer {
  position: absolute;
  z-index: 2;
  top: 8vh;
  padding: 50px;
}
</style>
