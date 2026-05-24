<script setup lang="ts">
import { usePlayerStore } from '@/stores/player';
import { useFavoritesStore } from '@/stores/favorites';
import SearchBar from '@/components/SearchBar.vue';
import PlayerFavoriteCard from '@/components/PlayerFavoriteCard.vue';
import { useRouter } from 'vue-router';
import { storeToRefs } from 'pinia';

const playerStore = usePlayerStore();
const { isLoading, error, query } = storeToRefs(playerStore);

const favoritesStore = useFavoritesStore();
const { favoritePlayers } = storeToRefs(favoritesStore);
const { maxFavoritePlayers } = favoritesStore;

const router = useRouter();

async function onQuery(): Promise<void> {
  const newRoute = await playerStore.fetchPlayer();
  router.push(newRoute);
}
</script>

<template>
  <section class="mt-8 mb-6 flex flex-col items-center gap-4 sm:mt-16 sm:mb-12">
    <h1 class="font-sans text-4xl font-bold">Skinviewer</h1>

    <SearchBar @query="onQuery" :is-loading v-model="query" placeholder="Enter playername" :error />
  </section>

  <hr class="h-0.5 w-full bg-slate-400 text-slate-400 dark:bg-slate-600 dark:text-slate-600" />

  <section class="mt-8 flex flex-col items-center gap-4 sm:mt-16">
    <div class="grid w-full items-center">
      <h2 class="justify-self-center font-sans text-2xl font-bold [grid-area:1/1]">Favorites</h2>
      <p v-if="favoritePlayers.length" class="justify-self-end rounded-sm bg-slate-200 px-1 [grid-area:1/1] dark:bg-slate-800">
        {{ favoritePlayers.length }}/{{ maxFavoritePlayers }}
      </p>
    </div>

    <div v-if="favoritePlayers.length" class="flex w-full flex-col gap-2 sm:grid sm:grid-cols-[repeat(auto-fit,minmax(24rem,1fr))] sm:gap-4">
      <PlayerFavoriteCard v-for="favorite in favoritePlayers" :key="favorite.meta.id" :favorite />
    </div>
    <div v-else class="flex w-full max-w-lg flex-col items-center gap-4 rounded-2xl border-2 border-accent p-4 shadow-center-md shadow-accent">
      <div class="text-center">
        <p>No favorites here.</p>
        <p>Search for players to add them to your favorites.</p>
      </div>
    </div>
  </section>
</template>
