<template>
  <v-app class="rounded rounded-md">
    <v-navigation-drawer>
      <v-list>
        <v-list-item title="Pessoas" to="/pessoas/1"></v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar title="Prodemge"></v-app-bar>

    <v-main class="d-flex my-12 justify-center" style="min-height: 300px;">
      <div style="width: 75%;">
        <Tabela />
      </div>
    </v-main>
  </v-app>
</template>
