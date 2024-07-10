<script setup>
import { BIconShop, BIconGearFill } from "bootstrap-icons-vue";
</script>

<script>
export default {
  props: {
    username: String,
  },
  data() {
    return {};
  },
  methods: {
    toggleSettings() {
      this.$emit("toggleSettings");
    },
    toggleShop() {
      this.$emit("toggleShop");
    },
  },
  mounted() {},
};
</script>

<template>
  <div class="navOuterContainer">
    <div class="navContainer">
      <div>
        <h2>Welcome {{ username }}</h2>
        <button>
          <BIconShop @click="toggleShop" />
        </button>
      </div>

      <button>
        <BIconGearFill @click="toggleSettings" />
      </button>
    </div>
  </div>
</template>

<style scoped>
.navOuterContainer {
  position: absolute;
  top: 0;
  z-index: 3;
}

.navContainer {
  width: 100vw;
  background-color: linear-gradient(
    180deg,
    rgba(0, 0, 0, 0.1) 0%,
    rgba(0, 0, 0, 0.1) 70.5%,
    rgba(0, 0, 0, 0) 100%
  );
  padding: 20px;
  display: flex;
  justify-content: space-between;
}
.navContainer div {
  display: flex;
  align-items: center;
  gap: 24px;
}

.navContainer button {
  background-color: transparent;
  border: none;
}
.navContainer svg {
  font-size: 30px;
  color: var(--white);
  cursor: pointer;
}
.navContainer h2 {
  color: var(--white);
  font-weight: 400;
  font-size: 20px;
}

/* Rectangle 21 */
</style>
