<template>
  <q-page class="row items-center justify-evenly">
    <example-component
      title="Example component"
      active
      :todos="todos"
      :meta="meta"
    ></example-component>
  </q-page>
</template>

<script setup lang="ts">
import { Todo, Meta } from 'components/models';
import ExampleComponent from 'components/ExampleComponent.vue';
import { ref, watch, onBeforeMount } from 'vue';
import { useQuery } from '@vue/apollo-composable';
import gql from 'graphql-tag';

// TODO: type the audioNFTs array
const audioNFTs = ref<any[]>([]);
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

const todos = ref<Todo[]>([
  {
    id: 1,
    content: 'ct1'
  },
  {
    id: 2,
    content: 'ct2'
  },
  {
    id: 3,
    content: 'ct3'
  },
  {
    id: 4,
    content: 'ct4'
  },
  {
    id: 5,
    content: 'ct5'
  }
]);
const meta = ref<Meta>({
  totalCount: 1200
});
</script>
