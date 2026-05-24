<script setup lang="ts">
import { type FunctionalComponent } from 'vue';
import MdiVuejs from '~icons/mdi/vuejs';
import MdiLanguageTypescript from '~icons/mdi/language-typescript';
import MdiTailwind from '~icons/mdi/tailwind';
import MdiApi from '~icons/mdi/api';

type Card = { label: string; text: string; url: string; icon: FunctionalComponent };

const cards = {
  vuejs: {
    label: 'Vuejs',
    text: 'A modern JavaScript framework for building web application user interfaces.',
    url: 'https://vuejs.org',
    icon: MdiVuejs,
  },
  typescript: {
    label: 'TypeScript',
    text: 'A JavaScript superset with full type safety and awesome tooling.',
    url: 'https://www.typescriptlang.org',
    icon: MdiLanguageTypescript,
  },
  cfWorkers: {
    label: 'Cloudflare Workers',
    text: 'A method of building fast and scalable backends in style of APIs.',
    url: 'https://workers.cloudflare.com',
    icon: MdiApi,
  },
  tailwindcss: {
    label: 'Tailwindcss',
    text: 'An intuitive utility-first CSS framework for building designs faster.',
    url: 'https://tailwindcss.com',
    icon: MdiTailwind,
  },
} as const satisfies Record<string, Card>;
</script>

<template>
  <section class="mt-8 mb-6 flex flex-col items-center gap-4 sm:mt-16 sm:mb-12">
    <h1 class="font-sans text-4xl font-bold">MC-Inspect</h1>

    <div class="w-full max-w-md text-center">
      <p>Welcome to MC-Inspect! A Minecraft player and server inspection web application.</p>
    </div>
  </section>

  <hr class="h-0.5 w-full bg-slate-400 text-slate-400 dark:bg-slate-600 dark:text-slate-600" />

  <section class="mt-8 flex flex-col items-center gap-2 sm:mt-16 sm:gap-4">
    <h2 class="font-sans text-2xl font-bold">About the project</h2>

    <div
      class="w-full max-w-3xl rounded-2xl border-2 border-accent p-4 text-center text-balance hover-smooth hover:shadow-center-md hover:shadow-accent sm:p-8 sm:text-justify sm:text-wrap"
    >
      <p>
        MC-Inspect lets you lookup various information about Minecraft players and servers. It is developed by nkmLABS as a learning exercise and now
        deployed as demo project for you to use. I (nkmLABS) build this web application to understand how to use different web technologies and tools.
        Especially, I acquired <span class="text-nowrap">knowledge of:</span>
      </p>
    </div>

    <div class="grid w-full max-w-3xl auto-rows-fr grid-cols-1 gap-2 sm:grid-cols-2 sm:gap-4">
      <a
        v-for="(card, key) in cards"
        :key
        :href="card.url"
        target="_blank"
        class="group flex flex-col gap-2 rounded-2xl border-2 border-accent p-4 hover-smooth hover:shadow-center-md hover:shadow-accent sm:gap-4 sm:p-8"
      >
        <div class="flex items-center gap-2 sm:gap-4">
          <div class="flex size-12 items-center justify-center rounded-2xl border-2 border-accent">
            <component :is="card.icon" class="size-8 text-accent transition-transform duration-400 ease-out group-hover:rotate-360"></component>
          </div>
          <p class="font-sans text-xl font-bold hover-smooth group-hover:text-accent">{{ card.label }}</p>
        </div>
        <p>{{ card.text }}</p>
      </a>
    </div>
  </section>
</template>
