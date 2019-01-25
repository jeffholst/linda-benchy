<template>
  <v-app>
    <v-toolbar app flat>
      <v-toolbar-title class="headline text-uppercase">
        <span>Linda</span>
        <span class="font-weight-light"> Benchy</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn flat @click="aboutClicked();">
        <span class="mr-2" >About</span>
      </v-btn>
    </v-toolbar>

    <v-content>
      <Control/>
    </v-content>
  </v-app>
</template>

<script>
import Control from './components/Control';

export default {
  name: 'App',
  components: {
    Control,
  },
  methods: {
    aboutClicked() {
      alert('About Clicked');
    },
  },
  data() {
    return {
      //
    };
  },
};
</script>
