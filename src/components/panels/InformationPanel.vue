<script setup>
import Card from "../Card.vue";

defineProps({
  active: Boolean,
});
</script>

<template>
  <Card title="Information" :active="active" />
</template>
