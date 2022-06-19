<script setup>
import { ref, watch } from 'vue';
import True from '@/assets/svg/True.vue';
import False from '@/assets/svg/False.vue';

const props = defineProps({
  isDisplayed: {
    type: Boolean,
    required: true
  },
  url: {
    type: String,
    required: true
  }
})

let innerValue_Display = ref(false);

watch(() => props.url, (val) => {
  innerValue_Display.value = (props.url.length > 0);
});
</script>

<template>
  <div class="display-flex direction-column flex-start" v-if="innerValue_Display">
    <span v-if="isDisplayed" class="align-center">
    <False class="small-size"/>
    </span>
    <span v-else class="align-center">
      <True class="small-size"/>
    </span>
  </div>
</template>

<style scoped>
.align-center {
  margin: 25px auto;
}

.small-size {
  width: 30px;
  height: 30px;
}
</style>