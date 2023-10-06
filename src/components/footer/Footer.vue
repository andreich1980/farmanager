<script setup>
import { ref } from "vue";
import Button from "./Button.vue";

const buttons = ref([
  { key: 1, title: "Help" },
  { key: 2, title: "UserMn" },
  { key: 3, title: "View" },
  { key: 4, title: "Edit" },
  { key: 5, title: "Copy" },
  { key: 6, title: "RenMov" },
  { key: 7, title: "MkFold" },
  { key: 8, title: "Delete" },
  { key: 9, title: "ConfMn" },
  { key: 10, title: "Quit" },
  { key: 11, title: "Plugin" },
  { key: 12, title: "Screen" },
]);
</script>

<template>
  <footer class="bg-black">
    <div class="text-gray">C:\></div>
    <div class="grid grid-cols-12 gap-3">
      <Button v-for="button in buttons" :button="button" />
    </div>
  </footer>
</template>
