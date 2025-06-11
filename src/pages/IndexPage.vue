<script setup lang="ts">
import { ref, watch, onMounted, nextTick } from 'vue';

interface Message {
  text: string;
  sent: boolean;
  time: string;
  name: string;
  avatar: string;
}

const USER = {
  name: 'Bạn',
  avatar: 'https://randomuser.me/api/portraits/men/85.jpg',
};
const BOT = {
  name: 'Bot',
  avatar: 'https://randomuser.me/api/portraits/lego/1.jpg',
};

const input = ref('');
const messages = ref<Message[]>([]);

const chatEndRef = ref<HTMLElement | null>(null);

// Đọc tin nhắn từ localStorage khi load trang
onMounted(() => {
  const saved = localStorage.getItem('chat-messages');
  if (saved) {
    messages.value = JSON.parse(saved);
  }
  void scrollToEnd();
});

// Lưu tin nhắn vào localStorage khi thay đổi
watch(
  messages,
  (val) => {
    localStorage.setItem('chat-messages', JSON.stringify(val));
    void scrollToEnd();
  },
  { deep: true },
);

async function scrollToEnd() {
  await nextTick();
  if (chatEndRef.value) {
    chatEndRef.value.scrollIntoView({ behavior: 'smooth' });
  }
}

function sendMessage() {
  if (!input.value.trim()) return;
  messages.value.push({
    text: input.value,
    sent: true,
    time: new Date().toLocaleTimeString(),
    name: USER.name,
    avatar: USER.avatar,
  });
  input.value = '';

  setTimeout(() => {
    messages.value.push({
      text: 'Bot trả lời: mình đã nhận được tin nhắn!',
      sent: false,
      time: new Date().toLocaleTimeString(),
      name: BOT.name,
      avatar: BOT.avatar,
    });
  }, 1000);
}
</script>

<template>
  <div class="q-pa-md column full-height">
    <div class="col scroll q-pa-sm" style="max-height: 75vh; overflow-y: auto">
      <q-chat-message
        v-for="(msg, index) in messages"
        :key="index"
        :text="[msg.text]"
        :sent="msg.sent"
        :stamp="msg.time"
        :name="msg.name"
        :avatar="msg.avatar"
      />
      <div ref="chatEndRef"></div>
    </div>

    <div class="col-auto row q-gutter-sm q-pt-md">
      <q-input
        outlined
        dense
        v-model="input"
        placeholder="Nhập tin nhắn..."
        class="col"
        @keyup.enter="sendMessage"
      />
      <q-btn label="Gửi" color="primary" @click="sendMessage" />
    </div>
  </div>
</template>
