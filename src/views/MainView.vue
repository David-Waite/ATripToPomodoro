<script setup>
import { getAuth, onAuthStateChanged } from "firebase/auth";
import LoopingBackground from "@/components/LoopingBackground.vue";
import { doc, getDoc, updateDoc } from "firebase/firestore";
import db from "@/main";

import TimerItem from "../components/TimerItem.vue";

import SettingsPopup from "@/components/SettingsPopup.vue";
</script>

<script>
export default {
  data() {
    return {
      auth: "",
      currentUserDocRef: null,
      userData: null,
      showSettings: false,
      stage: "start",
    };
  },
  methods: {
    toggleSettings() {
      this.showSettings = !this.showSettings;
    },
    // getting current users data
    async fetchUser() {
      this.userData = (
        await getDoc(doc(db, "users", this.auth.currentUser.uid))
      ).data();

      this.checkForVehiclesToAdd();
    },

    //Updates userfirestore with new vehicles
    async checkForVehiclesToAdd() {
      //getting and setting vehicles array
      const vehiclesSnapshot = await getDoc(
        doc(db, "vehicles", "FFtkXZWQgK9yTYWFTd7C")
      );
      let vehiclesData = vehiclesSnapshot.data();
      vehiclesData = vehiclesData["all"];
      //checks to see if any new vehicles have been added to the firebase, and updates the users firestore
      if (this.userData.vehiclesOwned.length != vehiclesData.length) {
        let vechiclesToBeAdded = this.userData.vehiclesOwned;
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

        await updateDoc(this.currentUserDocRef, {
          vehiclesOwned: vechiclesToBeAdded,
        });
      }
    },

    //SETTER for stage
    updateStage(newStage) {
      this.stage = newStage;
    },

    //Increases timestudying (pomodoro dollars)
    async updateTimeStudying() {
      const timeStudying =
        Number(this.userData.timeStudying) +
        Number(this.userData.settings.pomodoroTime * 60);

      await updateDoc(this.currentUserDocRef, { timeStudying: timeStudying });
      this.fetchUser();
    },

    //Buy vehicle (updating firestore)
    async buyVehicle(vehicle) {
      if (this.userData.timeStudying >= vehicle.price) {
        const vehicleIndex = this.userData.vehiclesOwned.findIndex(
          (v) => v.name === vehicle.name && v.price === vehicle.price
        );
        if (vehicleIndex !== -1) {
          this.userData.vehiclesOwned[vehicleIndex].status = "equip";
          await updateDoc(this.currentUserDocRef, {
            vehiclesOwned: this.userData.vehiclesOwned,
            timeStudying: this.userData.timeStudying - vehicle.price,
          });
        }

        this.fetchUser(this.auth.currentUser);
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
      await updateDoc(this.currentUserDocRef, {
        vehiclesOwned: this.userData.vehiclesOwned,
      });
    },
  },

  computed() {},
  watch: {
    userData: function (userData) {
      this.userData = userData;
    },
  },
  mounted() {
    this.auth = getAuth();
    onAuthStateChanged(this.auth, (user) => {
      if (!user) {
        this.$router.push("/login");
        return;
      }

      this.currentUserDocRef = doc(db, "users", this.auth.currentUser.uid);

      this.fetchUser();
    });
  },
};
</script>

<template>
  <div class="container" v-if="userData">
    <SettingsPopup
      :username="userData.username"
      :settings="userData.settings"
      :showSettings
      @toggleSettings="toggleSettings"
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
