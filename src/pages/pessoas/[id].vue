<template>
  <v-app class="rounded rounded-md">
    <v-navigation-drawer>
      <v-list>
        <v-list-item title="Pessoas" to="/pessoas"></v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar title="Prodemge"></v-app-bar>

    <v-main class="d-flex my-12 justify-center" style="min-height: 300px;">
      <div style="width: 75%;">
        <Enderecos />
      </div>
    </v-main>
  </v-app>
</template>
