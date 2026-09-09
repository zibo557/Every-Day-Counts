<template>
  <div class="page-wrap">
    <div class="container">
      <header class="page-header">
        <h1 class="main-title">时隅</h1>
        <p class="sub-title">时光一隅，记录每一段等待的时光</p>
      </header>

      <!-- 添加事件卡片 -->
      <div class="add-card">
        <div class="form-row">
          <input
            v-model="eventName"
            type="text"
            placeholder="写下你期待的事情..."
            class="input-item"
          />
          <input
            v-model="targetDate"
            type="date"
            class="input-item"
          />
          <input
            v-model="targetTime"
            type="time"
            class="input-item"
          />
          <button @click="addEvent" class="btn-add">➕ 添加期待</button>
        </div>
      </div>

      <!-- 倒计时列表 -->
      <div class="event-list">
        <!-- 空状态（页面没事件时，治愈提示，不再空白） -->
        <div v-if="eventList.length === 0" class="empty-block">
          <div class="empty-icon">☁️</div>
          <p>还没有期待的事件</p>
          <p class="empty-desc">添加第一件小事，开启你的倒计时</p>
        </div>

        <!-- 事件卡片循环 -->
        <div
          v-for="(item, index) in eventList"
          :key="index"
          class="event-card"
        >
          <div class="event-info">
            <h3 class="event-name">{{ item.name }}</h3>
            <p class="event-date">目标：{{ item.date }} {{ item.time }}</p>
          </div>
          <div class="count-box">
            <div class="day-num" :class="getDayClass(item.diffMs)">
              {{ item.days }}天 {{ item.hours }}时 {{ item.minutes }}分 {{ item.seconds }}秒
            </div>
            <span class="day-text">{{ item.diffMs >=0 ? '距离目标' : '已过去' }}</span>
          </div>
          <button @click="deleteEvent(index)" class="btn-delete">✕</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, onUnmounted } from 'vue'

// 从本地存储读取数据
const storageData = localStorage.getItem('countdownList')
const eventList = ref(storageData ? JSON.parse(storageData) : [])

const eventName = ref('')
const targetDate = ref('')
const targetTime = ref('00:00')

let timer = null

// 毫秒转 天、时、分、秒
function msToDHMS(ms) {
  const absMs = Math.abs(ms)
  const days = Math.floor(absMs / (1000 * 60 * 60 * 24))
  const hours = Math.floor((absMs % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
  const minutes = Math.floor((absMs % (1000 * 60 * 60)) / (1000 * 60))
  const seconds = Math.floor((absMs % (1000 * 60)) / 1000)
  return { days, hours, minutes, seconds }
}

// 刷新全部倒计时
function refreshAllCountdown() {
  const now = new Date()
  eventList.value.forEach(item => {
    const targetDateTime = new Date(`${item.date} ${item.time}`)
    const diffMs = targetDateTime - now
    const timeObj = msToDHMS(diffMs)
    item.diffMs = diffMs
    item.days = timeObj.days
    item.hours = timeObj.hours
    item.minutes = timeObj.minutes
    item.seconds = timeObj.seconds
  })
}

// 挂载：立刻刷新 + 开启每秒定时器
onMounted(() => {
  refreshAllCountdown()
  timer = setInterval(refreshAllCountdown, 1000)
})

// 组件销毁，清除定时器（防止内存泄漏）
onUnmounted(() => {
  clearInterval(timer)
})

// 监听eventList变化，自动保存到localStorage
watch(eventList, (newVal) => {
  localStorage.setItem('countdownList', JSON.stringify(newVal))
}, { deep: true })

// 添加事件
const addEvent = () => {
  if (!eventName.value || !targetDate.value || !targetTime.value) return
  const targetDateTime = new Date(`${targetDate.value} ${targetTime.value}`)
  const now = new Date()
  const diffMs = targetDateTime - now
  const timeObj = msToDHMS(diffMs)

  eventList.value.push({
    name: eventName.value,
    date: targetDate.value,
    time: targetTime.value,
    diffMs: diffMs,
    days: timeObj.days,
    hours: timeObj.hours,
    minutes: timeObj.minutes,
    seconds: timeObj.seconds
  })
  // 清空输入框
  eventName.value = ''
  targetDate.value = ''
  targetTime.value = '00:00'
}

// 删除事件
const deleteEvent = (idx) => {
  eventList.value.splice(idx, 1)
}

// 根据毫秒差值返回样式class
const getDayClass = (diffMs) => {
  if (diffMs > 0) return 'normal'
  if (diffMs === 0) return 'today'
  return 'expired'
}
</script>

<style scoped>
/* 全局页面 奶油治愈背景 */
.page-wrap {
  min-height: 100vh;
  padding: 48px 20px;
  box-sizing: border-box;
  background: linear-gradient(160deg, #fff7f0 0%, #f0f7f4 100%);
}

.container {
  max-width: 740px;
  margin: 0 auto;
}

.page-header {
  text-align: center;
  margin-bottom: 36px;
}
.main-title {
  font-size: 32px;
  color: #5c544b;
  margin:0 0 8px;
  font-weight: 500;
}
.sub-title {
  color: #948b80;
  font-size: 16px;
  margin:0;
}

/* 添加事件卡片 */
.add-card {
  background: rgba(255,255,255,0.72);
  padding:26px;
  border-radius: 22px;
  box-shadow: 0 4px 14px rgba(180,160,140,0.08);
  margin-bottom: 24px;
}
.form-row {
  display: flex;
  gap:12px;
  flex-wrap: wrap;
  align-items: center;
}
.input-item {
  padding:12px 16px;
  color: #000000;
  border:1px solid #b6c4d4;
  border-radius:14px;
  outline: none;
  font-size:15px;
  background: #fff;
  flex:1;
  min-width:140px;
}
.input-item:focus {
  border-color: #7399c2;
}
.btn-add {
  padding:12px 20px;
  border:none;
  border-radius:14px;
  background: #d8c8b8;
  color:#5c544b;
  cursor: pointer;
  transition: 0.2s;
  font-size:15px;
}
.btn-add:hover {
  background: #c8b9a9;
}

/* 日期、时间选择器图标样式，修改选择器小图标颜色，区分背景 */
input[type="date"]::-webkit-calendar-picker-indicator,
input[type="time"]::-webkit-calendar-picker-indicator {
  filter: invert(0.85) sepia(0.3) hue-rotate(5deg) saturate(0.5);
  cursor: pointer;
}
input[type="date"]::-webkit-calendar-picker-indicator:hover,
input[type="time"]::-webkit-calendar-picker-indicator:hover {
  background: #f3e4d2;
  border-radius: 8px;
}

/* 事件列表 */
.event-list {
  display: flex;
  flex-direction: column;
  gap:18px;
}

/* 空状态样式，解决页面空白 */
.empty-block {
  text-align:center;
  padding:70px 20px;
  color:#b4a89b;
}
.empty-icon {
  font-size:52px;
  margin-bottom:16px;
}
.empty-desc {
  font-size:14px;
  margin-top:4px;
}

/* 每一条倒计时卡片 */
.event-card {
  background: rgba(255,255,255,0.75);
  padding:22px 26px;
  border-radius:20px;
  box-shadow:0 3px 12px rgba(180,160,140,0.07);
  display:flex;
  justify-content: space-between;
  align-items:center;
  transition: 0.25s ease;
}
.event-card:hover {
  transform: translateY(-3px);
  box-shadow:0 8px 18px rgba(180,160,140,0.11);
}
.event-name {
  margin:0;
  color:#5c544b;
  font-size:19px;
  font-weight:500;
}
.event-date {
  margin:4px 0 0;
  color:#a89c8f;
  font-size:14px;
}
.count-box {
  text-align:center;
}
.day-num {
  font-size:22px;
  font-weight:600;
}
.day-text {
  font-size:14px;
  color:#a89c8f;
}
/* 不同状态颜色 */
.normal {
  color:#b98b79;
}
.today {
  color:#e29b4b;
}
.expired {
  color:#a06c60;
}

.btn-delete {
  width:32px;
  height:32px;
  border:none;
  border-radius: 50%;
  background: #f1e9e2;
  color:#947c70;
  cursor:pointer;
  font-size:16px;
}
.btn-delete:hover {
  background: #e8d9cf;
}
</style>
