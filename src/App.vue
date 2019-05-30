<template>
  <v-app id="app">
    <v-content>
      <v-container id="container">
        <router-view></router-view>
      </v-container>
    </v-content>
  </v-app>
</template>

<style lang="stylus" scope>
#app
  background: #f5f5f5

#container
  max-width: 550px
  padding: 8px

.primary--text {
  color: #e86a52 !important;
}

.primary {
  background-color: #e64074 !important;
  border-color: #e64074 !important;
}
</style>
