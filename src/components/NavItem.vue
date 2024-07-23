<script setup>
import {
  BIconShop,
  BIconGearFill,
  BIconPersonCircle,
} from "bootstrap-icons-vue";
</script>

<script>
export default {
  props: {
    username: String,
    loggedIn: Boolean,
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
    toggleAccount() {
      this.$emit("toggleAccount");
    },
  },
  mounted() {},
};
</script>

<template>
  <div class="navOuterContainer">
    <div class="navContainer">
      <div
        :class="!loggedIn ? 'iconContainerLoggedIn' : 'iconContainerLoggedOut'"
      >
        <button v-if="!loggedIn">
          <p>login or sign up</p>
          <BIconPersonCircle @click="toggleAccount" />
        </button>
        <button>
          <p>shop</p>
          <BIconShop @click="toggleShop" />
        </button>
      </div>

      <div>
        <button>
          <p>setting</p>
          <BIconGearFill @click="toggleSettings" />
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.navOuterContainer {
  position: absolute;
  top: 0;
  z-index: 4;
}
.navContainer button p {
  display: none;
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
}
.iconContainerLoggedOut {
  gap: 24px;
}
.iconContainerLoggedIn {
  gap: 12px;
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
