<template>
  
  <div class="app-container">
    
    <div v-if="!isNewPage">
      <div v-if="begging" class="welcome-popup">
        <div class="welcomewelcome1">
        <div class="title">
          <p>欢迎使用今日运势计算器</p>
          <p>计算过程基于严谨的哈希算法，绝对科学</p>
          <p>免责声明：计算结果与作者无关，真倒霉了别怪我</p>
          <p>本计算器由WWG个人开发，再偷必究</p>
          <p>点击 '开始' 按钮以开始</p>
        </div>
        <button class="clickToClose" @click="begging = false">开始</button>
       </div>
    </div>
      <div class="header">
        <div class="title">请输入姓名和出生日期</div>
        <input class="name" v-model="name" type="text" placeholder="Enter your name" />
        <div class="date-input">
          <input class="year" v-model="year" type="number" placeholder="YYYY" maxlength="4" />
          <span>-</span>
          <input class="month" v-model="month" type="text" placeholder="MM" maxlength="2" />
          <span>-</span>
          <input class="day" v-model="day" type="text" placeholder="DD" maxlength="2" />
        </div>
        <button class="clickToSubmit" @click="handleClick">Submit</button>
      </div>
    </div>
    <div v-else>
      <div class="new-page">
        <div class="header1">
          <button class="clickToBack" @click="goBack">Back</button>
          <div class="title1">
            <div class="welcomewelcome">你好, {{ name }}!</div>
            <div class="birthday">你的生日是: {{ year }}-{{ month }}-{{ day }}</div>
          </div>
          <div class="todayinput">
            <input class="todayMM" v-model="todaymonth" type="text" placeholder="请输入今天是几月" />
            <input class="todayDD" v-model="todayday" type="text" placeholder="请输入今天是几号" />
            <input class="weather" v-model="todayweather" type="text" placeholder="你觉得今天天气怎么样？" />
            <button class="circle-btn" @click="handlepackage">点击</button>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div v-if="isFortuneVisible" class="fortune-popup">
    <div class="popup-content">
      <h2>今日运势</h2>
      <p>{{ fortune }}</p>
      <button @click="isFortuneVisible = false">关闭</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import CryptoJS from 'crypto-js';// 引入 CryptoJS 库用于计算运势
const name = ref('');
const year = ref('');
const month = ref('');
const day = ref('');
const todaymonth = ref('');
const todayday = ref('');
const todayweather = ref('');
const fortune = ref('');
//切换下一页的棋子变量
const isNewPage = ref(false);
//弹窗
const isFortuneVisible = ref(false);
const begging = ref(true); // 初始状态为 true，表示显示欢迎界面
const saveToLocalStorage1 = () => {
  const userInfo0 = {
    todayMonth: todaymonth.value,
    todayDay: todayday.value,
    weather: todayweather.value
  };
  localStorage.setItem('userInfo0', JSON.stringify(userInfo0));
  console.log('信息已保存到本地存储:', userInfo0);
};
const saveToLocalStorage = () => { // 保存用户信息到本地存储
  const userInfo = {
    name: name.value,
    date: `${year.value}-${month.value}-${day.value}`
  };
  localStorage.setItem('userInfo', JSON.stringify(userInfo));
  console.log('信息已保存到本地存储:', userInfo);
};
// 计算运势的函数
const calculateFortune = () => {
  const data = `${name.value}${year.value}${month.value}${day.value}${todaymonth.value}${todayday.value}${todayweather.value}`;
  const hash = CryptoJS.MD5(data).toString();
  const hashValue = parseInt(hash.slice(0, 2), 16);
  const fortunes = [
    '大吉！天上掉馅饼，拐角遇到爱。',
    '中吉！虽然没撞大运，但是撞小运了。',
    '小吉！平平淡淡的一天...',
    '小灾！会有些波折，但总体而言是差强人意的一天。',
    '中灾！避免冲动，留个心眼！',
    '大灾！运势低迷，需多加小心！',
    '老天爷今天要搞你心态了！不宜做重大决策，静待时机。'
  ];
  const index = hashValue % fortunes.length;
  fortune.value = fortunes[index];
}
const handlepackage = () => {
  if (!isValidInput01()) {
    return; // 如果输入不合法，直接返回
  }
  isFortuneVisible.value = true; // 显示运势弹窗
  saveToLocalStorage1();
  calculateFortune(); // 计算运势
};
const handleClick = () => {
  if (!isValidInput()) {
    return; // 如果输入不合法，直接返回
  }
  // 如果输入合法，保存信息并切换页面
  saveToLocalStorage();
  isNewPage.value = true; // 切换到新页面
};
const goBack = () => {
  isNewPage.value = false; // 返回到原页面
};
//判断输入是否合法
const isValidInput = () => {
  const chineseRegex = /^[\u4e00-\u9fa5]+$/; // 中文字符正则表达式
  if (!chineseRegex.test(name.value)) {
    alert('请输入中文姓名!');
    return false;
  }
  const yearRegex = /^(19|20)\d{2}$/; // 年份正则表达式
  if (!yearRegex.test(year.value)) {
    alert('请输入有效的年份!');
    return false;
  }
  const monthRegex = /^(0[1-9]|1[0-2])$/; // 月份正则表达式
  if (!monthRegex.test(month.value)) {
    alert('请输入有效的月份!');
    return false;
  }
  const dayRegex = /^(0[1-9]|[12][0-9]|3[01])$/;
  if (!dayRegex.test(day.value)) {
    alert('请输入有效的日期!');
    return false;
  }
  return true; // 所有输入都合法
};
const isValidInput01 = () => {
  const chineseRegex = /^[\u4e00-\u9fa5]+$/; // 中文字符正则表达式
  if (!chineseRegex.test(todayweather.value)) {
    alert('请在天气栏目里面输入中文内容!');
    return false;
  }
  const monthRegex = /^(0[1-9]|1[0-2])$/; // 月份正则表达式
  if (!monthRegex.test(todaymonth.value)) {
    alert('请输入有效的月份!');
    return false;
  }
  const dayRegex = /^(0[1-9]|[12][0-9]|3[01])$/;
  if (!dayRegex.test(todayday.value)) {
    alert('请输入有效的日期!');
    return false;
  }
  return true; // 所有输入都合法
};
</script>

<style>
body {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  margin: 0;
}

.app-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.header {
  width: 250px;
  height: 150px;
  background-color: aliceblue;
  border-radius: 15px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.title {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 2px;
}

.date-input {
  display: flex;
  justify-content: center;
  align-items: center;
}

.name {
  width: 200px;
  height: 30px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.year {
  width: 68px;
  height: 20px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.month,
.day {
  width: 50px;
  height: 20px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.clickToSubmit {
  width: 100px;
  height: 30px;
  border-radius: 5px;
  border: none;
  background-color: #4caf6d;
  color: rgb(253, 252, 252);
  font-weight: 700;
  cursor: pointer;
  margin-bottom: 3px;
  font-size: medium;
  cursor: pointer;
  transition: background-color 0.2s;
}

.clickToSubmit:active {
  background-color: #0056b3;
  /* 按钮被点击时改变背景颜色 */
}

.new-page {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 500px;
  height: 100vh;
  flex-direction: column;
}

.header1 {
  width: 350px;
  height: 500px;
  background-color: aliceblue;
  border-radius: 15px;
  display: flex;
  align-items: center;
  flex-direction: column;
  position: relative;
}

.title1 {
  font-size: 20px;
  font-weight: bold;
  margin-top: 1px;
  margin: 7px;
}

.welcomewelcome {
  font-size: 30px;
  font-weight: bold;
  text-align: center;
  margin-top: 30px;
}

.birthday {
  margin-top: 1px;
  font-size: 24px;
  text-align: center;
  font-style: inherit;
}

.clickToBack {
  position: absolute;
  width: 80px;
  height: 30px;
  border-radius: 5px;
  border: none;
  background-color: #4caf6d;
  color: rgb(253, 252, 252);
  font-weight: 700;
  cursor: pointer;
  font-size: medium;
  transition: background-color 0.2s;
  top: 10px;
  left: 10px;
}

.clickToBack:active {
  background-color: #0056b3;
  /* 按钮被点击时改变背景颜色 */
}

.todayMM {
  width: 250px;
  height: 20px;
  margin: 0;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.todayDD {
  width: 250px;
  height: 20px;
  margin: 0;
  margin-top: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.weather {
  width: 250px;
  height: 60px;
  margin: 0;
  margin-top: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.todayinput {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.circle-btn {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  background: rgb(185, 21, 21);
  color: white;
  border: none;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.2s;
  margin-top: 60px;
}

.circle-btn:active {
  background-color: #0056b3;
}

.circle-btn:hover {
  background-color: #45a049;
}

.circle-btn:focus {
  outline: none;
}

.todayDD,
.todayMM,
:focus {
  outline: none;
}

.fortune-popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.popup-content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  text-align: center;
}

.popup-content button {
  margin-top: 10px;
}
.welcome-popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1001; /* 设置一个较高的 z-index 值，确保弹窗覆盖其他元素 */
}
.welcomewelcome1 {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.welcome-popup-title {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.welcome-popup .title {
  text-align: center;
  font-family: Arial, sans-serif;
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 10px;
}
.clickToClose {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  border: none;
  background-color: #4caf6d;
  color: rgb(253, 252, 252);
  font-weight: 700;
  cursor: pointer;
  font-size: 35px;
  transition: background-color 0.2s;
}
.clickToClose:active {
  background-color: #0056b3;
  /* 按钮被点击时改变背景颜色 */
}
.clickToClose:hover {
  background-color: #45a049;
}
.clickToClose:focus {
  outline: none;
}
</style>
