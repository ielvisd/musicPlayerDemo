<template>
    <div
    >
      <audio-track :track="track" v-for="track in tracks" :key="track.origin" />
    </div>
</template>

<script setup lang="ts">
import AudioTrack from './AudioTrack.vue';
import { Track } from './models';

interface Props {
  tracks?: Track[];
}
const props = withDefaults(defineProps<Props>(), {
  tracks: () => [],
});
</script>
