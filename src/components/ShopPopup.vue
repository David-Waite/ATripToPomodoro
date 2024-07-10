<script setup>
import PopupCard from "../components/UI/PopupCard.vue";
import { BIconXCircle } from "bootstrap-icons-vue";
import Classic from "../assets/images/vehicles/classic/classicStill.png";
import TheGhost from "../assets/images/vehicles/ghost/ghostStill.png";
</script>
<script>
export default {
  props: {
    auth: Object,
    showShop: Boolean,
    userData: Object,
  },
  data() {
    return {};
  },
  methods: {
    toggleShop() {
      this.$emit("toggleShop");
    },
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {
    console.log(this.userData.vehiclesOwned);
  },
};
</script>
<template>
  <PopupCard v-if="showShop">
    <div class="header">
      <div class="coinContainer">
        <img src="../assets/images/pomodoroCoin.png" alt="pomodoroCoins" />
        <p>
          {{ userData.timeStudying }}
        </p>
      </div>

      <h1>SHOP</h1>
      <BIconXCircle @click="toggleShop" />
    </div>
    <h2 class="categoryHeading">Vehicles:</h2>
    <div class="categoryContainer">
      <div
        class="itemCard"
        v-for="(vehicle, index) in userData.vehiclesOwned"
        :key="index"
      >
        <h3>{{ vehicle.name }}</h3>

        <img
          class="vehicleImage"
          :src="
            (vehicle.name == `Classic` && Classic) ||
            (vehicle.name == `Ghost` && TheGhost)
          "
          alt="{{ vehicle.name }}"
        />
        <div class="buttonContainer">
          <button
            class="buy btn"
            v-if="vehicle.status == 'buy'"
            @click="$emit(`buyVehicle`, vehicle)"
          >
            <img src="../assets/images/pomodoroCoin.png" alt="pomodoroCoins" />
            ${{ vehicle.price }}
          </button>
          <button class="equipped btn" v-if="vehicle.status == 'equipped'">
            Equipped
          </button>
          <button
            class="equip btn"
            v-if="vehicle.status == 'equip'"
            @click="$emit(`equipVehicle`, vehicle)"
          >
            Equip
          </button>
        </div>
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
.header svg,
.header p {
  font-size: 24px;
  font-weight: 500;
  color: var(--textPrimary);
}
.coinContainer {
  display: flex;
  align-items: center;
  gap: 8px;
}
.coinContainer img {
  height: 30px;
}
.categoryHeading {
  font-size: 24px;
  color: var(--textPrimary);
  font-weight: 500;
}
.categoryContainer {
  display: flex;
  gap: 16px;
  margin: 16px 0;
}
.itemCard {
  border: 4px solid var(--primary);
  border-radius: 12px;
  background-color: var(--white);
  width: 240px;
  padding-top: 16px;
}
.itemCard h3 {
  text-align: center;
  color: var(--grey);
}
.vehicleImage {
  width: 100%;
}
.buttonContainer {
  background-color: var(--cardFilling);
  display: flex;
  align-items: center;
  justify-content: center;
}
.buttonContainer img {
  height: 20px;
}
.btn {
  width: 160px;
  height: 32px;
  border: none;
  margin: 10px;
  cursor: pointer;
  border-radius: 8px;
  font-size: 18px;
  font-family: RobotoMono;
  font-weight: 800;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  transition: 0.3s ease-in-out;
}
.buy {
  background-color: var(--white);
  color: var(--textPrimary);
}
.buy:hover {
  background-color: var(--gold);
  color: var(--white);
}
.equipped {
  background-color: var(--green);
  color: var(--white);
  cursor: default;
}
.equip {
  background-color: var(--grey);
  color: var(--white);
}
.equip:hover {
  background-color: #919191;
}
</style>
