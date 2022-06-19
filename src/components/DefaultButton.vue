<script setup>
defineProps({
  text: {
    type: String,
    required: true
  }
})
</script>

<template>
  <div class="display-flex column flex-start">
    <button class="submit-button" type="button">{{ text }}</button>
  </div>
</template>

<style scoped>
.submit-button {
  display: inline-block;
  width: 60px;
  margin: 20px 10px;
  padding: 10px;
  text-align: center;
  transition: 0.3s;
  font-weight: 500;
  border: 0;
  box-shadow: 0 5px 5px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
  background-color: rgba(76, 175, 80, 0.8);
  color: white;
}
.submit-button:hover {
  background-color: rgba(76, 175, 80, 0.5);
}
</style>
