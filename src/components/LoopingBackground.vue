<script setup>
import ClassicMoving from "../assets/images/vehicles/classic/classicMoving.gif";
import ClassicStill from "../assets/images/vehicles/classic/classicStill.png";

import TheGhostMoving from "../assets/images/vehicles/ghost/ghostMoving.gif";
import TheGhostStill from "../assets/images/vehicles/ghost/ghostStill.png";
</script>
<template>
  <div class="outerContainer">
    <img
      class="image background"
      :class="getStage"
      src="../assets/images/backgrounds/sunsetDrive/background.png"
      alt="image background"
    />

    <img
      class="image foreground"
      :class="getStage"
      src="../assets/images/backgrounds/sunsetDrive/foreground.png"
      alt="image forground"
    />

    <img
      class="imageBackface"
      src="../assets/images/backgrounds/sunsetDrive/backface.png"
      alt="imageBackface"
    />

    <img
      class="vehicle"
      :class="stage != 'running' && 'vehicleMoving'"
      :src="
        (getEquippedVehicleName() == `Classic` && ClassicMoving) ||
        (getEquippedVehicleName() == `Ghost` && TheGhostMoving)
      "
      :alt="getEquippedVehicleName()"
    />
    <img
      v-if="stage === 'paused' || stage === 'start'"
      class="vehicle"
      :src="
        (getEquippedVehicleName() == `Classic` && ClassicStill) ||
        (getEquippedVehicleName() == `Ghost` && TheGhostStill)
      "
      :alt="getEquippedVehicleName()"
    />
  </div>
  <p class="credits">
    <a target="_blank" href="https://www.cassiefleming.com/"
      >Artist: Cassie Fleming</a
    >&nbsp;|&nbsp;
    <a target="_blank" href="https://www.davidwaitedev.com/"
      >Developer: David Waite</a
    >
  </p>
</template>

<script>
export default {
  props: {
    stage: String,
    vehicles: Array,
  },
  computed: {
    getStage() {
      return this.stage;
    },
  },
  methods: {
    getEquippedVehicleName() {
      const equippedVehicle = this.vehicles.findIndex(
        (v) => v.status === "equipped"
      );
      return this.vehicles[equippedVehicle].name;
    },
  },
};
</script>

<style scoped>
.credits {
  position: absolute;
  bottom: 20px;
  display: flex;
  right: 40px;

  color: var(--white);

  font-weight: 100;
  font-size: 12px;
  z-index: 3;
}
.credits a {
  text-decoration: none;
  color: var(--white);
}
.outerContainer {
  display: flex;
}
.vehicle {
  position: absolute;
  left: 0;
  top: 45vh;
  height: 40vh;
  left: 50vw;
  transform: translateX(-50%);
  z-index: 2;
}
.vehicleMoving {
  height: 30vh;
  top: 50vh;
  display: none;
}

.foreground {
  animation: studyTimeForeground 9s linear infinite;
  transform: translateX(-100%);
  position: relative;
  z-index: 3;
  top: 0;
}
.background {
  animation: studyTimeBackground 9s linear infinite;
  z-index: 2;
}

.imageBackface {
  position: absolute;
  z-index: 1;
  height: 100vh;

  top: 0;
  left: 0;
}
.outerContainer {
  position: absolute;
  top: 0px;
  left: 0;
}
.image {
  height: 100vh;
}

.start {
  animation-play-state: paused;
}
.paused {
  animation-play-state: paused;
}
.running {
  animation-play-state: running;
}

@keyframes studyTimeForeground {
  0% {
    transform: translateX(-100%);
  }

  100% {
    transform: translateX(-150%);
  }
}
@keyframes studyTimeBackground {
  0% {
    transform: translateX(0);
  }

  100% {
    transform: translateX(-50%);
  }
}
@media (max-width: 600px) {
  .credits {
    align-items: center;
    justify-content: center;

    right: inherit;
    font-size: 11px;
  }
}
</style>
