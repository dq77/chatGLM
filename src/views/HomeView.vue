<script lang="ts" setup>
import { ref } from 'vue'
import { fetchEventSource } from '@microsoft/fetch-event-source'

const input = ref('')
const messageList = ref([])

const handleEnter = () => {
  messageList.value.push({
    role: 'user',
    content: input.value
  })
  sendMessage()
  input.value = ''
}

const sendMessage = async () => {
  const index = messageList.value.length
  messageList.value.push({
    role: 'assistant',
    content: ''
  })
  const url = ['https://op', 'en.bigmode', 'l.cn/api/pa', 'as/v4/ch', 'at/comp', 'letions'].join('')
  const auth = ['Bear', 'er 07', '7ed094b30cb', '945b5cff798', 'bb294020.eIuk', 'EPlevmGsfkhO'].join('')
  // 发送消息逻辑
  fetchEventSource(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': auth
    },
    body: JSON.stringify({
      model: 'glm-4-flash',
      messages: messageList.value,
      stream: true
    }),
    onmessage(event) {
      if (event.data === '[DONE]') { return }
      messageList.value[index].content += JSON.parse(event.data).choices[0].delta.content
    },
    onclose() {
      console.log('关闭连接');
    },
    onerror(error) {
      console.info(error);
    }
  })
}
</script>

<template>
  <main class="main-ctx">
    <div :class="`main-list ${messageList.length === 0}`">
      <el-scrollbar>
        <div v-for="(item, index) in messageList" :key="index" :class="`item-content`">
          <div v-if="item.role === 'user'"></div>
          <div :class="`${item.role}`">{{ item.content }}</div>
        </div>
      </el-scrollbar>
    </div>
    <div class="bottom">
      <el-input v-model="input" @keyup.enter="handleEnter" placeholder="想知道什么，来问问我~" size="large" />
      <div :class="`footer-desc ${messageList.length === 0}`">以上内容均由AI生成, 仅供参考和借鉴<br/>copyright © 2021-2024  辽ICP备2024045160号</div>
    </div>
  </main>
</template>

<style lang="scss">
html, body, #app {
  height: 100vh;
  margin: 0;
}
.main-ctx{
  height: 100%;
  max-width: 800px;
  margin: 0 auto;
  padding: 0 15px;
  background-color: #f6f7f9;
  .main-list{
    height: calc(100vh - 120px);
    padding: 15px 0;
    &.true {
      height: calc(50vh - 60px);
    }
    .el-scrollbar__view{
      padding-bottom: 20px;
    }
    .item-content{
      color: #1a2029;
      font-size: 18px;
      line-height: 32px;
      display: flex;
      justify-content: space-between;
      .user{
        border-radius: 16px;
        padding: 10px 18px;
        margin-bottom: 15px;
        background-color: #e6f7ff;
        color: #1890ff;
      }
      .assistant{
        border-radius: 16px;
        padding: 10px 18px;
        margin-bottom: 15px;
        margin-right: 50px;
        background-color: #fff;
      }
    }
  }
  .bottom{
    height: 90px;
    .el-input--large{
      font-size: 16px;
      .el-input__inner{
        height: 44px;
        line-height: 44px;
      }
    }
    .footer-desc{
      font-size: 12px;
      text-align: center;
      color: #b0b7c0;
      padding: 8px 0 4px;
      &.true{
        margin-top: calc(50vh - 60px);
      }
    }
  }
}
@media (min-width: 800px) {

  html {
    background-color: rgb(246, 247, 249);
  }

}
</style>
