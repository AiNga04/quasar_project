<template>
  <q-layout view="hHh lpR fFf">
    <!-- Header -->
    <q-header elevated class="bg-primary text-white">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          class="q-mr-sm"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />
        <q-avatar>
          <img src="https://randomuser.me/api/portraits/men/85.jpg" alt="avatar" />
        </q-avatar>
        <q-toolbar-title class="q-ml-sm">Chat App</q-toolbar-title>
        <q-space />
        <q-btn flat dense icon="logout" label="Logout" @click="logout" />
      </q-toolbar>
    </q-header>

    <!-- Sidebar -->
    <q-drawer v-model="leftDrawerOpen" show-if-above bordered :breakpoint="600">
      <q-list>
        <q-item-label header>Danh sách người dùng</q-item-label>
        <q-item
          v-for="user in users"
          :key="user.id"
          clickable
          :active="user.id === selectedUserId"
          @click="selectUser(user.id)"
        >
          <q-item-section avatar>
            <q-avatar>
              <img :src="user.avatar" />
            </q-avatar>
          </q-item-section>
          <q-item-section>
            <div class="text-weight-medium">{{ user.name }}</div>
            <div class="text-caption text-grey" v-if="getLastMessage(user.id)">
              {{ getLastMessage(user.id)?.text }}
            </div>
          </q-item-section>
          <q-item-section side v-if="getLastMessage(user.id)">
            <div class="text-caption text-grey">
              {{ getTimeAgo(getLastMessage(user.id)?.time) }}
            </div>
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <!-- Main chat content -->
    <q-page-container>
      <router-view :selected-user="selectedUser" :users="users" />
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

const leftDrawerOpen = ref(false);

const users = ref([
  { id: 1, name: 'Nguyễn Văn A', avatar: 'https://randomuser.me/api/portraits/men/32.jpg' },
  { id: 2, name: 'Trần Thị B', avatar: 'https://randomuser.me/api/portraits/women/44.jpg' },
  { id: 3, name: 'Lê Văn C', avatar: 'https://randomuser.me/api/portraits/men/65.jpg' },
]);

const selectedUserId = ref(users.value[0]?.id ?? null);
const selectedUser = computed(() => users.value.find((u) => u.id === selectedUserId.value));

// Lấy tin nhắn cuối cùng từ localStorage
function getLastMessage(userId: number) {
  const key = `chat-messages-${userId}`;
  const messages = JSON.parse(localStorage.getItem(key) || '[]');
  return messages.length > 0 ? messages[messages.length - 1] : null;
}

// Tính thời gian "x phút trước"
function getTimeAgo(timeString: string | undefined) {
  if (!timeString) return '';
  const now = new Date();
  const today = now.toLocaleDateString();
  const dateTime = new Date(`${today} ${timeString}`);
  const diff = Math.floor((now.getTime() - dateTime.getTime()) / 60000); // phút
  if (diff < 1) return 'Vừa xong';
  if (diff < 60) return `${diff}p trước`;
  const hours = Math.floor(diff / 60);
  return `${hours}g trước`;
}

function selectUser(id: number) {
  selectedUserId.value = id;
}

function logout() {
  alert('Bạn đã đăng xuất!');
}
</script>
