<template>
  <div class="flex justify-center w-full min-h-screen py-10 bg-surface-ground">
    <Card class="w-full max-w-4xl">
      <template #title>Survey creator</template>
      <template #subtitle>By Ansis Sprugevics</template>

      <template #content>
        <Button icon="pi pi-plus" label="Add Question" />
      </template>
    </Card>
  </div>
</template>

<script setup lang="ts">
import { useLocalStorage } from "@vueuse/core";
import Card from "primevue/card";
import Button from "primevue/button";
import { Ref } from "vue";

const savedState: Ref<any[]> = useLocalStorage("edurio-survey", []);
</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
