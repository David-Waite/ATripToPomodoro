<script>
export default {
  props: {
    visable: Boolean,
    earnedAmount: Number,
  },
};
</script>

<template>
  <div class="container" :class="visable && 'moveDown'">
    <p>+</p>
    <div class="coinContainer">
      <img src="../../assets/images/pomodoroCoin.png" alt="pomodoroCoins" />
      <p>{{ earnedAmount }}</p>
    </div>
  </div>
</template>

<style scoped>
.container {
  position: absolute;
  top: -50px;
  display: flex;
  gap: 8px;
  max-width: 140px;
  background-color: var(--green);
  border-radius: 50px;
  z-index: 2;
  color: var(--white);
  padding: 5px;
  font-weight: 400;
  font-size: 20px;
  transition: 0.4s ease-in-out;
}
.moveDown {
  top: 20px;
}
.container img {
  height: 24px;
}
.coinContainer {
  display: flex;
  align-items: center;
  gap: 4px;
}
</style>
