<template>
  <q-layout view="hHh lpR fFf">
    <!-- Header -->
    <q-header elevated class="bg-primary text-white">
      <q-toolbar>
        <q-avatar>
          <img src="https://randomuser.me/api/portraits/men/85.jpg" alt="avatar" />
        </q-avatar>
        <q-toolbar-title class="q-ml-sm">Chat App</q-toolbar-title>
        <q-space />
        <q-btn flat dense icon="logout" label="Logout" @click="logout" />
      </q-toolbar>
    </q-header>

    <!-- Sidebar -->
    <q-drawer show-if-above bordered>
      <q-list>
        <q-item-label header>Danh sách người dùng</q-item-label>
        <q-item v-for="user in users" :key="user.id" clickable>
          <q-item-section avatar>
            <q-avatar>
              <img :src="user.avatar" />
            </q-avatar>
          </q-item-section>
          <q-item-section>{{ user.name }}</q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <!-- Main chat content -->
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const users = ref([
  { id: 1, name: 'Nguyễn Văn A', avatar: 'https://randomuser.me/api/portraits/men/32.jpg' },
  { id: 2, name: 'Trần Thị B', avatar: 'https://randomuser.me/api/portraits/women/44.jpg' },
  { id: 3, name: 'Lê Văn C', avatar: 'https://randomuser.me/api/portraits/men/65.jpg' },
]);

function logout() {
  // Xử lý logout ở đây
  alert('Bạn đã đăng xuất!');
}
</script>
