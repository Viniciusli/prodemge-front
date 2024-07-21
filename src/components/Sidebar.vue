<template>
  <v-navigation-drawer class="w-[15%] rounded-lg bg-deep-purple">
    <div class="pa-4 rounded-te-xl text-center">
      <div class="mt-4">Prodemge</div>
    </div>

    <v-list>
      <v-list-item
        v-for="(item, i) in links"
        :key="i"
        :value="item"
        active-class="border"
        :ripple="false"
      >
        <template v-slot:prepend>
          <v-icon :icon="item.icon" ></v-icon>
        </template>

        <v-list-item-title v-text="item.text"></v-list-item-title>
      </v-list-item>
    </v-list>
  </v-navigation-drawer>
</template>

<script setup>
import { ref } from "vue";
const links = [
  { text: "Pessoas", icon: "mdi mdi-account-outline" },
];

const drawer = ref(null);
</script>
