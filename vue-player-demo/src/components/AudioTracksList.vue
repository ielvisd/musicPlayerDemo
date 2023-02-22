<template>
  <div>
    <p mt-4 v-if="!currentTrack"> The current tracks are loading, please wait</p>

    <AudioPlayer v-if="currentTrack" :track="currentTrack" mt-4 @loadedmetadata="playTrack" ref="audioPlayer" :option="{
      src: currentTrackLocation,
      title: currentTrack.name,
      coverImage: currentTrackImageLocation,
    }" />

    <audio-track @setCurrentTrack="loadAndOrPlayPauseCurrentTrack" :track="track" v-for="track in tracks"
      :key="track.origin" />

  </div>
</template>

<script setup lang="ts">
import AudioTrack from './AudioTrack.vue';
import { Track } from './models';
import { ref, watch, withDefaults } from 'vue';
import AudioPlayer from 'vue3-audio-player'
import 'vue3-audio-player/dist/style.css'

const currentTrack = ref<Track | null>(null);
const currentTrackLocation = ref('');
const currentTrackImageLocation = ref('');
const audioPlayer = ref(AudioPlayer)

const playTrack = () => {
  audioPlayer?.value?.play();
}

// This will load a track if it's not already loaded, and play/pause it if it is
const loadAndOrPlayPauseCurrentTrack = (track: Track) => {
  if (currentTrack.value?.origin === track.origin) {
    audioPlayer.value.isPlaying ? audioPlayer.value.pause() : audioPlayer.value.play();
  } else {
    currentTrack.value = track;
    currentTrackImageLocation.value = `https://berry2.relayx.com/${track.image}`

    const currentTrackLocationFromOrigin = currentTrack.value.origin.replace(/.$/, '2');

    currentTrackLocation.value = `https://staging-backend.relayx.com/api/berry/${currentTrackLocationFromOrigin}`;
  }
}


interface Props {
  tracks?: Track[];
}
const props = withDefaults(defineProps<Props>(), {
  tracks: () => [],
});

watch(props, () => {
  loadAndOrPlayPauseCurrentTrack(props.tracks[0]);
});

</script>
