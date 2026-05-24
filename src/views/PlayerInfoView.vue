<script setup lang="ts">
import { usePlayerStore } from '@/stores/player';
import SearchBar from '@/components/SearchBar.vue';
import CopyButton from '@/components/CopyButton.vue';
import FavoriteToggle from '@/components/FavoriteToggle.vue';
import { downloadFileFromURL } from '@/utils/download';
import { computed, defineAsyncComponent } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { storeToRefs } from 'pinia';
import type { Player, FavoritePlayer } from '@/types';
const SkinView = defineAsyncComponent(() => import('@/components/SkinCanvas.vue'));

const playerStore = usePlayerStore();
const { isLoading, data, query } = storeToRefs(playerStore);

const route = useRoute();
const router = useRouter();
const currentLocation = computed<string>(() => window.location.origin + route.fullPath);

type PlayerInfo = { label: string };

const playerInfos = {
  name: { label: 'Name' },
  uuid: { label: 'UUID' },
  skinId: { label: 'Skin ID' },
  playerModel: { label: 'Model' },
  skinUrl: { label: 'Skin URL' },
  capeUrl: { label: 'Cape URL' },
} as const satisfies Record<keyof Player, PlayerInfo>;

async function onQuery(): Promise<void> {
  const newRoute = await playerStore.fetchPlayer();
  router.push(newRoute);
}

const currentPlayer = computed<FavoritePlayer | null>(() => {
  if (!data.value) return null;
  return {
    meta: {
      type: 'player',
      id: data.value.uuid,
    },
    data: {
      name: data.value.name,
      uuid: data.value.uuid,
    },
  };
});
</script>

<template>
  <template v-if="data">
    <section class="mt-8 mb-6 flex flex-col items-center gap-4 sm:mt-16 sm:mb-12">
      <h1 class="font-sans text-4xl font-bold">Skinviewer</h1>

      <SearchBar @query="onQuery" :is-loading v-model="query" placeholder="Enter playername" />

      <div class="flex w-full max-w-lg flex-col gap-2 sm:grid sm:grid-cols-2 sm:gap-4">
        <FavoriteToggle v-if="currentPlayer" :favorite="currentPlayer" class="btn-reverse" />
        <CopyButton :text="currentLocation" label="link" class="btn-reverse" />

        <button @click="downloadFileFromURL(data.skinUrl, `${data.name}_skin.png`)" class="btn-reverse">Download Skin</button>
        <button v-if="data.capeUrl" @click="downloadFileFromURL(data.capeUrl, `${data.name}_cape.png`)" class="btn-reverse">Download Cape</button>
        <button v-else disabled class="btn-reverse hidden sm:inline">Download Cape</button>
      </div>
    </section>

    <hr class="h-0.5 w-full bg-slate-400 text-slate-400 dark:bg-slate-600 dark:text-slate-600" />

    <section class="relative mt-8 flex flex-col items-center gap-4 sm:mt-16">
      <h2 class="font-sans text-2xl font-bold">{{ data.name }}</h2>

      <div class="flex w-full flex-col-reverse items-center gap-4 lg:grid lg:grid-cols-[1fr_auto]">
        <div class="hidden h-full flex-col justify-between gap-4 sm:flex sm:max-w-xl lg:max-w-none">
          <template v-for="(playerInfo, key) in playerInfos" :key>
            <div
              v-if="data[key]"
              class="flex flex-col gap-4 rounded-2xl border-2 border-slate-400 bg-gray-200 p-4 dark:border-slate-600 dark:bg-gray-900"
            >
              <p class="text-center font-mono text-xl">{{ playerInfo.label }}</p>

              <hr class="h-0.5 w-full bg-slate-400 text-slate-400 dark:bg-slate-600 dark:text-slate-600" />

              <div class="grid grid-cols-[auto_1fr] items-center gap-4">
                <CopyButton :text="data[key]" class="btn-reverse w-20" />
                <p class="overflow-scroll border-l-2 border-slate-400 pl-4 text-nowrap text-ellipsis dark:border-slate-600">{{ data[key] }}</p>
              </div>
            </div>
          </template>
        </div>
        <div
          class="flex w-full max-w-lg flex-col rounded-2xl border-2 border-slate-400 bg-gray-200 p-4 sm:hidden dark:border-slate-600 dark:bg-gray-900"
        >
          <template v-for="(playerInfo, key) in playerInfos" :key>
            <div v-if="data[key]" class="grid items-center gap-y-4 border-slate-400 py-4 not-last:border-b-2 dark:border-slate-600">
              <CopyButton :text="data[key]" class="btn-reverse w-20 justify-self-start [grid-area:1/1]" />
              <p class="justify-self-center font-mono text-xl [grid-area:1/1]">{{ playerInfo.label }}</p>
              <p class="overflow-scroll border-l-2 border-slate-400 pl-2 text-nowrap text-ellipsis [grid-area:2/1] dark:border-slate-600">
                {{ data[key] }}
              </p>
            </div>
          </template>
        </div>

        <SkinView />
      </div>
    </section>
  </template>
</template>
