<template>
  <div min-h-screen>
    <div class="q-pa-md mx-auto" style="max-width: 300px">
      <div class="q-gutter-md">
        <q-select v-model="dateModel" :options="options"  :display-value="`${dateModel ? dateModel.label : '*none*'}`"
        />
      </div>
    </div>

    <AudioPlayer
      v-if="currentTrack"
      @loadedmetadata="playTrack"
      mt-4
      @play="checkIsPlaying"
      @pause="checkIsPlaying"
      ref="bitcoinAudioPlayer"
      :option="{
        src: currentTrackLocation,
        title: currentTrack.name,
        coverImage: currentTrackImageLocation,
      }"
    />

    <p v-if="!tracks.length">No tracks sold during selected timeframe.</p>

    <audio-track
      @setCurrentTrack="loadAndOrPlayPauseCurrentTrack"
      :track="track"
      :isPlaying="isPlaying"
      v-for="track in tracks"
      :key="track.origin"
    />
  </div>
</template>

<script setup lang="ts">
import AudioTrack from './AudioTrack.vue';
import { Track } from './models';
import { ref, watch, withDefaults } from 'vue';
import AudioPlayer from 'vue3-audio-player';
import 'vue3-audio-player/dist/style.css';

const currentTrack = ref<Track | null>(null);
const currentTrackLocation = ref('');
const currentTrackImageLocation = ref('');
const bitcoinAudioPlayer = ref(AudioPlayer);
const isPlaying = ref(false);

const emit = defineEmits<{
  (e: 'setNewDate', newDateModel: object): void
}>()

const dateModel= ref( {
    label: 'Last 7 days',
    value: '7d'
  })
const options = [
  {
    label: 'Last 24 hours',
    value: '24h'
  },
  {
    label: 'Last 7 days',
    value: '7d'
  },
  {
    label: 'Last 30 days',
    value: '30d'
  },
  {
    label: 'All time',
    value: 'total'
  }
]

// This function will be called when a new date is selected
watch(dateModel, (newDateModel) => {
  console.log('model', dateModel, newDateModel)
  emit('setNewDate', newDateModel)
})

const playTrack = () => {
  bitcoinAudioPlayer?.value?.play();
};

const checkIsPlaying = () => {
  console.log('checkIsPlaying', bitcoinAudioPlayer.value.isPlaying)
  bitcoinAudioPlayer.value.isPlaying ? (isPlaying.value = true) : (isPlaying.value = false);
}

// Maybe just a load here
const loadCurrentTrack = (track: Track) => {
  console.log('track is', track)
  // Handle no sales error
  if(!track) {
    currentTrack.value = {
      name: 'No sales in current range',
      origin: ''
    },
    currentTrackLocation.value = '',
    currentTrackImageLocation.value = 'https://picsum.photos/200';
    return
  }

  currentTrack.value = track;
  currentTrackImageLocation.value = `https://berry2.relayx.com/${track.image}`;
  const currentTrackLocationFromOrigin = currentTrack.value.origin.replace(
    /.$/,
    '2'
  );
  currentTrackLocation.value = `https://staging-backend.relayx.com/api/berry/${currentTrackLocationFromOrigin}`;
};

// This will load a track if it's not already loaded, and play/pause it if it is
const loadAndOrPlayPauseCurrentTrack = (track: Track) => {
  console.log('track is', track)
  loadCurrentTrack(track);
  console.log('currentTrack is', currentTrack.value)
  console.log('bitcoinAudioPlayer is', bitcoinAudioPlayer?.value, currentTrack.value?.origin === track.origin && bitcoinAudioPlayer.value.currentTime > 0)
  console.log('currentTrack.value?.origin === track.origin', currentTrack.value?.origin === track.origin)
  console.log(currentTrack.value?.origin === track.origin && bitcoinAudioPlayer.value.play !== undefined)

  console.log(currentTrack.value?.origin === track.origin && bitcoinAudioPlayer?.value)
  if (currentTrack.value?.origin === track.origin && typeof bitcoinAudioPlayer.value.play === 'function')  {
    bitcoinAudioPlayer.value.isPlaying
      ? bitcoinAudioPlayer.value.pause()
      : bitcoinAudioPlayer.value.play();
  }
};

interface Props {
  tracks?: Track[];
}
const props = withDefaults(defineProps<Props>(), {
  tracks: () => [],
});

watch(currentTrack, (newTrack) => {
  loadAndOrPlayPauseCurrentTrack(newTrack ? newTrack : {name: 'No sales in current range', origin: ''});
});
</script>
