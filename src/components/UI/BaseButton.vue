<template>
  <button :type="type" :class="mode">
    <slot></slot>
  </button>
</template>

<script>
export default {
  props: ["type", "mode"],
};
</script>

<style scoped>
button {
  padding: 0.75rem 1.5rem;
  background: #3a0061;
  border: 1px solid #3a0061;
  color: white;
  cursor: pointer;
}

button:hover,
button:active {
  background: #270041;
  border: #270041;
}

.flat {
  background: transparent;
  color: #3a0061;
  border: none;
}

.flat:hover,
.flat:active {
  background: #edd2ff;
}
</style>