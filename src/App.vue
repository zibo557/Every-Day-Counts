<template>
  <div class="container">
    <h1>倒计时备忘录</h1>
    <div class="input-area">
      <input v-model="eventName" placeholder="输入备忘录事件名称" />
      <input v-model="targetDateTime" type="datetime-local" />
      <button @click="addItem">添加倒计时</button>
    </div>

    <div id="listWrap">
      <div class="item" v-for="item in listData" :key="item.id">
        <div class="item-info">
          <h3>{{ item.title }}</h3>
          <div class="count-text">
            距离目标：<span class="cd-text">{{ item.countStr }}</span>
          </div>
        </div>
        <button class="del-btn" @click="delItem(item.id)">删除</button>
      </div>
    </div>
  </div>
</template>

<script setup>

</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Microsoft Yahei", sans-serif;
}

.container {
  max-width: 650px;
  margin: 40px auto;
  background: #ffffff;
  padding: 30px;
  border-radius: 14px;
  box-shadow: 0 4px 14px rgba(64,158,255,0.12);
}
h1 {
  text-align: center;
  color: #1976d2;
  margin-bottom: 28px;
}
.input-area {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  margin-bottom: 24px;
}
input {
  padding: 10px 12px;
  border:1px solid #b3d8fa;
  border-radius: 8px;
  font-size: 15px;
  background-color: #d4ecff;
  color:#d24b0d;
}
input:nth-child(1) {
  flex: 1;
  min-width: 160px;
}
button {
  padding: 10px 22px;
  background-color: #409EFF;
  color: white;
  border: none;
  border-radius:8px;
  cursor: pointer;
  transition: 0.2s;
}
button:hover {
  background-color: #66b1ff;
}
.item {
  padding: 18px;
  border:1px solid #cce7ff;
  border-radius: 10px;
  margin-bottom: 14px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #ffffff;
}
.item-info h3 {
  font-size:17px;
  color:#0f4c81;
  margin-bottom:6px;
}
.count-text {
  color:#f56c6c;
  font-weight:bold;
  font-size:16px;
}
.del-btn {
  padding:6px 14px;
  background-color:#ff7875;
  color:#fff;
  border:none;
  border-radius:6px;
  cursor:pointer;
}
.del-btn:hover {
  background-color:#ff9494;
}

/*时间框单独样式*/
.time-input {
  background-color: #ffffff;
  border: 1px solid #ffb6c1;
  min-width: 230px;
}
:deep(.time-input::-webkit-calendar-picker-indicator) {
  background-color: #ffb6c1;
  padding: 5px;
  border-radius: 4px;
  cursor: pointer;
}
:deep(.time-input::-webkit-calendar-picker-indicator:hover) {
  background-color: #ff8fab;
}

</style>
