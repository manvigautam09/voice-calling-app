<template>
  <VoiceCall />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import VoiceCall from "./components/VoiceCall.vue";

export default defineComponent({
  name: "App",
  components: {
    VoiceCall,
  },
});
</script>

<style></style>
