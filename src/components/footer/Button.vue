<script setup>
defineProps({ button: Object });
</script>

<template>
  <button class="flex">
    <span class="text-gray">{{ button.key }}</span>
    <span class="bg-green text-left grow truncate">{{ button.title }}</span>
  </button>
</template>
