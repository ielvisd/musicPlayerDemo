<template>
  <q-page class="row items-center justify-evenly">
    <audio-tracks-list
      @setNewDate="getAudioRankings"
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
const getAudioRankings = async (dateFilter: object) => {
  console.log('getAudioRankings called', dateFilter)
  const { result } = useQuery(gql`
    query Ranks{
      ranks(
      filter: "${dateFilter.value}",
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
  audioNFTs.value = result.value?.ranks;

  watch(result, (newResult) => {
    audioNFTs.value = newResult.ranks;
  });
};

// Get audio NFTs before mount
onBeforeMount(() => {
  getAudioRankings({
    label: 'Last 7 days',
    value: '7d'
  });
});
</script>
