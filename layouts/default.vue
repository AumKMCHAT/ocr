<template>
  <v-app dark>
    <v-app-bar
    fixed
    >
    <v-toolbar-title><v-icon>mdi-camera-iris</v-icon>  OCR Web</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer
    fixed
    >
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
   
    }
  }
}
</script>
