<script setup>
defineProps({
  title: String,
  active: Boolean,
});
</script>

<template>
  <div class="relative px-1 py-3">
    <div
      class="absolute top-px w-fit line-clamp-1 px-2 left-1/2 -translate-x-1/2 font-medium"
      :class="active ? 'bg-green text-black' : 'bg-blue text-light-blue'"
    >
      {{ title }}
    </div>

    <div class="border border-light-blue p-px h-full">
      <div class="border border-light-blue h-full">
        <slot />
      </div>
    </div>
  </div>
</template>
