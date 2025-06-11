<script setup lang="ts">
import { ref, watch, onMounted, nextTick, computed } from 'vue';

// Nhận prop selectedUser từ MainLayout
const props = defineProps<{
  selectedUser: { id: number; name: string; avatar: string } | undefined;
  users: Array<{ id: number; name: string; avatar: string }>;
}>();

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

const input = ref('');
const allMessages = ref<Record<number, Message[]>>({});

const chatEndRef = ref<HTMLElement | null>(null);

function getStorageKey(userId: number) {
  return `chat-messages-${userId}`;
}

// Load messages for selected user
function loadMessages(userId: number) {
  const saved = localStorage.getItem(getStorageKey(userId));
  allMessages.value[userId] = saved ? JSON.parse(saved) : [];
}

// Khi mounted hoặc đổi user thì load lại messages
onMounted(() => {
  if (props.selectedUser) {
    loadMessages(props.selectedUser.id);
    void scrollToEnd();
  }
});

watch(
  () => props.selectedUser?.id,
  (userId) => {
    if (userId !== undefined) {
      loadMessages(userId);
      void scrollToEnd();
    }
  },
  { immediate: true },
);

// Lưu tin nhắn vào localStorage khi thay đổi
watch(
  allMessages,
  () => {
    if (props.selectedUser) {
      localStorage.setItem(
        getStorageKey(props.selectedUser.id),
        JSON.stringify(allMessages.value[props.selectedUser.id] || []),
      );
      void scrollToEnd();
    }
  },
  { deep: true },
);

async function scrollToEnd() {
  await nextTick();
  if (chatEndRef.value) {
    chatEndRef.value.scrollIntoView({ behavior: 'smooth' });
  }
}

const BOT_REPLIES = [
  'Mình đã nhận được tin nhắn của bạn!',
  'Bạn cần hỗ trợ gì thêm không?',
  'Cảm ơn bạn đã nhắn tin!',
  'Bot đang lắng nghe bạn đây.',
  'Bạn có thể hỏi mình bất cứ điều gì.',
];

function getRandomBotReply() {
  const idx = Math.floor(Math.random() * BOT_REPLIES.length);
  return BOT_REPLIES[idx];
}

function sendMessage() {
  if (!input.value.trim() || !props.selectedUser) return;
  const userId = props.selectedUser.id;
  if (!allMessages.value[userId]) allMessages.value[userId] = [];
  allMessages.value[userId].push({
    text: input.value,
    sent: true,
    time: new Date().toLocaleTimeString(),
    name: USER.name,
    avatar: USER.avatar,
  });
  input.value = '';

  setTimeout(() => {
    if (!allMessages.value[userId]) allMessages.value[userId] = [];
    allMessages.value[userId].push({
      text: `Bot trả lời cho ${props.selectedUser?.name ?? 'Người dùng'}: ` + getRandomBotReply(),
      sent: false,
      time: new Date().toLocaleTimeString(),
      name: props.selectedUser?.name ?? 'Người dùng',
      avatar: props.selectedUser?.avatar ?? 'https://randomuser.me/api/portraits/lego/1.jpg',
    });
  }, 1000);
}

// Chỉ lấy messages của user đang chọn
const messages = computed(() => {
  if (!props.selectedUser) return [];
  return allMessages.value[props.selectedUser.id] || [];
});
</script>

<template>
  <div class="q-pa-md column full-height">
    <!-- Hiển thị tên người dùng đang chat -->
    <div
      v-if="props.selectedUser"
      class="text-h6 text-primary q-mb-md q-pt-xs q-pb-xs"
      style="text-align: center"
    >
      {{ props.selectedUser.name }}
    </div>
    <div class="col scroll q-pa-sm" style="max-height: 77vh; overflow-y: auto">
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
