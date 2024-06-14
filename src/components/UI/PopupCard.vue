<script></script>

<template>
  <div class="popUpContainer">
    <slot></slot>
  </div>
</template>

<style scoped>
.popUpContainer {
  position: absolute;
  width: 98vw;
  height: 95vh;
  z-index: 5;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--background);
  border: 6px solid var(--backgroundBorder);
  border-radius: 40px;
  padding: 40px;
}
</style>
