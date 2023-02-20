<template>
    <div
    >
      <p mt-4 v-if="currentTrack"> The current track is: {{ currentTrack.name }}</p>
      <p mt-4 v-else> The current tracks are loading, please wait</p>

      <AudioPlayer
        v-if="currentTrack"
        :option="{
          src: currentTrackLocation,
          title: currentTrack.name,
          coverImage: currentTrackImageLocation,
        }"
      />

      <audio-track @setCurrentTrack="setLoadAndPlayCurrentTrack" :track="track" v-for="track in tracks" :key="track.origin" />

    </div>
</template>

<script setup lang="ts">
import AudioTrack from './AudioTrack.vue';
import { Track } from './models';
import { ref, watch } from 'vue';
import AudioPlayer from 'vue3-audio-player'
import 'vue3-audio-player/dist/style.css'

const currentTrack = ref<Track | null>(null);
const currentTrackLocation = ref('');
const currentTrackImageLocation = ref('');

const setLoadAndPlayCurrentTrack = (track: Track) => {
  currentTrack.value = track;
  currentTrackImageLocation.value = `https://berry2.relayx.com/${track.image}`


  const currentTrackLocationFromOrigin = currentTrack.value.origin.replace(/.$/, '2');

  currentTrackLocation.value = `https://staging-backend.relayx.com/api/berry/${currentTrackLocationFromOrigin}`;
};

interface Props {
  tracks?: Track[];
}
const props = withDefaults(defineProps<Props>(), {
  tracks: () => [],
});

watch(props, () => {
  setLoadAndPlayCurrentTrack(props.tracks[0]);
});

</script>
