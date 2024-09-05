<script setup>
import { ref, onMounted } from 'vue';
import {owo, logger} from 'owotools';
import Swal from 'sweetalert2';
import 'sweetalert2/src/sweetalert2.scss';
import axios from 'axios';
import DefaultButton from '@/components/DefaultButton.vue';
import ShowValidity from '@/components/ShowValidity.vue';
import githubLogo from '@/assets/img/github-mark-white.svg';

const apiUrl = 'http://localhost:8080/api/owol';

let originalUrl = ref('');
let display     = ref(false);
const totalVisits = ref(0);

const forbiddenDomains = [
  'http://localhost/',
  'http://127.0.0.1/',
  'https://owol.cc/'
];

onMounted(async () => {
  try {
    const response = await axios.get(apiUrl + '/visit-stats');
    totalVisits.value = response.data.totalVisits;
  } catch (error) {
    console.error('Failed to fetch visit count:', error);
  }
});

function isForbiddenURL(url) {
  return forbiddenDomains.some(domain => url.startsWith(domain));
}

function checkValidity() {
  originalUrl.value = originalUrl.value.trim();
  let url = originalUrl.value;
  if(url.length == 0) {
    display.value = false;
    return 0;
  }
  display.value = !owo.isValidUrl(url);
}

function onSubmit() {
  if(checkValidity() === 0) {
    Swal.fire({
      title: '出错了惹!',
      text: '请检查填写的URL地址是否为空!',
      icon: 'warning',
      confirmButtonText: 'OK...'
    });
    logger.info('URL为空!');
    return;
  }

  if(display.value === true) {
    Swal.fire({
      title: '格式不正确噢!',
      text: '填写的URL不合法, 请仔细检查后再次输入~',
      icon: 'error',
      confirmButtonText: '耶比( •̀ ω •́ )y'
    });
    logger.error('请验证URL的可用性!');
    return;
  }

  if(isForbiddenURL(originalUrl.value)) {
    Swal.fire({
      title: '错误!',
      text: '无法缩短该URL.',
      icon: 'error',
      confirmButtonText: 'OK'
    });
    return;
  }

  Swal.fire({
    title: '请求提交成功!',
    text: '即将快递给你新鲜出炉的短链接, 请耐心等待!',
    icon: 'success',
    confirmButtonText: '好的!( •̀ ω •́ )~'
  });


  axios.post(apiUrl + '/url-check', {
    url: originalUrl.value
  })
  .then((response) => {
    const result = response.data.result;

    if (result) {
      const result = response.data.result;
      const shortUrl = response.data.short_url;
      Swal.fire({
        title: '===执行结果===',
        text: '你的短链接在此! ' + shortUrl,
        icon: 'success',
        confirmButtonText: "多谢啦(●'◡'●)"
      });
      logger.success('缩短URL成功! 新的短链接: ' + shortUrl);
    } else {
      Swal.fire({
        title: '===执行结果===',
        text: '抱歉，链接无法访问: ' + originalUrl.value,
        icon: 'error',
        confirmButtonText: '了解了...'
      });
      logger.error('URL 无法访问!');
    }
  })
  .catch((error) => {
    Swal.fire({
      title: '请求出错!',
      text: error.response ? error.response.data.message : '未知错误',
      icon: 'error',
      confirmButtonText: 'OK...'
    });
    logger.error('请求出错: ' + (error.response ? error.response.data.message : '未知错误'));
  });

}
</script>

<template>
  <!-- GitHub Logo -->
  <a href="https://github.com/Tommy131/OwOLink/" target="_blank" class="github-logo">
    <img :src="githubLogo" alt="GitHub Logo">
  </a>

  <div class="container">
    <div class="inner-container flex-center direction-column">
      <div class="title-box"><h1>OwOLink - 快速分享你的网址</h1></div>
      <div class="input-container">
        <div class="display-flex direction-row space-between">
          <ShowValidity :isDisplayed="display" :url="originalUrl" />
          <div class="full-size display-flex direction-column">
            <input placeholder="输入需要分享的原始URL..." v-model="originalUrl" @input="checkValidity" @keyup.enter="onSubmit" />
            <transition><p v-if="display" class="label-invalid">当前输入的URL地址不合法!</p></transition>
          </div>
          <DefaultButton text="提交" @click="onSubmit" />
        </div>
      </div>
    </div>
  </div>

  <!-- 底部版权信息和访问统计 -->
  <footer class="footer">
      <div class="copyright">
        <p>&copy; 2024 <b><a href="https://owoblog.com/blog/" target="_blank">OwOTeam</a></b> - <b>OwOLink</b>. All rights reserved.</p>
        <p>Website visits: {{ totalVisits }}</p>
      </div>
    </footer>
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

/* GitHub Logo 样式 */
.github-logo {
  position: absolute;
  top: 20px;
  right: 20px;
  z-index: 1000; /* 确保图标在最上层 */
}
.github-logo img {
  width: 40px; /* 调整为合适的图标大小 */
  height: 40px;
  border: 0;
}

.footer {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  color: white;
  text-align: center;
  padding: 10px 0;
}

.footer .copyright p {
  margin: 0;
}
</style>