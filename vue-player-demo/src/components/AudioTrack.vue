<template>
      <q-card
      class="my-card" border-solid border-0 border-b border-blue-900 flat w-full md:w-lg>
        <q-card-section horizontal>
          <q-card-section>
              <p my-auto text-xs font-bold>   {{ track.stats.rank }}.</p>
            </q-card-section>

          <div class="w-24 my-auto px-4">
              <q-img
                class="col-5 shadow rounded-full max-w-full h-auto align-middle border-none"
                style="height: 50px; max-width: 50px"
                :src="`https://berry2.relayx.com/${track.image}`"
              />
          </div>

          <q-card-section >
            <p my-auto font-bold> {{ track.name }}</p>
          </q-card-section>

          <q-card-section ml-auto>
            <p my-auto text-xs font-bold>   {{ track.stats.vol_total / 100000000 }}</p>
          </q-card-section>

        </q-card-section>
      </q-card>
</template>

<script setup lang="ts">
import { Track } from './models';

interface Props {
  track: Track;
}
const props = withDefaults(defineProps<Props>(), {
  track: () => { name: '' },
});

</script>
