<script setup>
import { ref } from "vue";
import Card from "./components/Card.vue";
import Footer from "./components/footer/Footer.vue";
import FileManagerPanel from "./components/panels/FileManagerPanel.vue";
import InformationPanel from "./components/panels/InformationPanel.vue";

// https://www.farmanager.com/screenshots.php
const activeCard = ref("left");
</script>

<template>
  <div class="flex min-h-screen flex-col bg-blue font-mono font-medium">
    <main class="grow h-1">
      <div class="grid md:grid-cols-2 h-full">
        <FileManagerPanel
          :active="activeCard === 'left'"
          @click="activeCard = 'left'"
        />

        <InformationPanel
          class="hidden md:block"
          :active="activeCard === 'right'"
          @click="activeCard = 'right'"
        />
      </div>
    </main>

    <Footer />
  </div>
</template>
