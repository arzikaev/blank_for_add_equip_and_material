<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <h1> SITY-STROY</h1>
    </v-app-bar>
    <v-main>
      <Blank/>
    </v-main>
  </v-app>
</template>

<script>
import Blank from './components/Blank';

export default {
  name: 'App',

  components: {
    Blank,
  },

  data: () => ({
    //
  }),
};
</script>
