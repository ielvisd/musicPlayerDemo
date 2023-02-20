<template>
  <q-page class="row items-center justify-evenly">
    <audio-tracks-list
      title="Example component"
      active
      :tracks="audioNFTs"
    ></audio-tracks-list>
  </q-page>
</template>

<script setup lang="ts">
import { Track } from 'components/models';
import AudioTracksList from 'src/components/AudioTracksList.vue';
import { ref, watch, onBeforeMount } from 'vue';
import { useQuery } from '@vue/apollo-composable';
import gql from 'graphql-tag';

// TODO: type the audioNFTs array
const audioNFTs = ref<Track[]>([]);
const getAudioRankings = async () => {
  const { result } = useQuery(gql`
    query Ranks{
      ranks(
      filter: "total",
      category: "AUDIO",
      search: "") {
        name,
        image,
        origin,
        stats {
          rank
          floor
          vol_24h
          vol_7d
          vol_30d
          vol_total
          __typename
      },
        __typename
      }
    }
  `);

  watch(result, (value) => {
      audioNFTs.value = value?.ranks;
    }
  );
};

// Get audio NFTs before mount
onBeforeMount(() => {
  getAudioRankings();
});



</script>
