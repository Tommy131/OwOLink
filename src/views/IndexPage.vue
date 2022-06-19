<script setup>
import { ref } from 'vue';
import {owo, logger} from 'owotools';
import DefaultButton from '@/components/DefaultButton.vue';
import ShowValidity from '@/components/ShowValidity.vue';

let originalUrl = ref('');
let display     = ref(false);

function checkValidity() {
  let url = originalUrl.value;
  if(url.length == 0) {
    display.value = false;
    return;
  }
  display.value = !owo.isValidUrl(url);
}

</script>

<template>
  <div class="container">
    <div class="inner-container flex-center direction-column">
      <div class="title-box"><h1>OwOLink - 快速分享你的网址</h1></div>
      <div class="input-container">
        <div class="display-flex direction-row space-between">
          <ShowValidity :isDisplayed="display" :url="originalUrl" />
          <div class="full-size display-flex direction-column">
            <input placeholder="输入需要分享的原始URL..." v-model="originalUrl" @input="checkValidity" />
            <transition><p v-if="display" class="label-invalid">当前输入的URL地址不合法!</p></transition>
          </div>
          <DefaultButton text="提交" />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
input {
  margin: 10px;
  padding: 20px 10px;
  text-align: center;
  transition: 0.3s;
  box-shadow: 0 5px 11px rgba(0, 0, 0, 0.3);
  border: 0;
  border-radius: 30px;
  background-color: rgba(0, 0, 0, 0.5);
  color: white;
}
input:hover, input:focus {
  border-color: #00dcff;
  outline: 0;
  -webkit-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px #00dcff;
	box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px #00dcff;
}

.inner-container {
  width: 60%;
  margin: auto auto;
}

.title-box {
  height: 50px;
  text-align: center;
  font-weight: bold;
  color: white;
}
.title-box h1 {
  position: relative;
  display: inline-block;
  margin-bottom: 20px;
  padding-bottom: 5px;
  transition: 0.3s;
}
.title-box h1::before {
  position: absolute;
  content: '';
  width: 0%;
  height: 5px;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  transition: 0.3s;

  border-bottom: 5px solid #00dcff;
}
.title-box h1:hover::before {
  width: 100%;
}

.full-size {
  width: 1500px;
}

.input-container {
  position: relative;
  width: 100%;
  margin: 10px;
  padding: 20px;
  transition: 0.3s;
  box-shadow: 0 5px 11px rgba(0, 0, 0, 0.3);
  border-radius: 30px;
  background-color: rgba(10, 32, 50, 0.7);
}

.label-invalid {
  display: inline-block;
  margin-right: auto;
  margin-left: 10px;
  padding: 10px;
  border-radius: 10px;
  background: rgba(255, 0, 0, 0.4);
  color: white;
}


@media screen and (max-width: 1500px) {
  .inner-container {
    width: 85%;
  }
}

@media screen and (max-width: 760px) {
  .title-box {
    margin-bottom: 35px;
  }
}

@media screen and (max-width: 500px) {
  .inner-container {
    width: 100%;
  }
}
</style>