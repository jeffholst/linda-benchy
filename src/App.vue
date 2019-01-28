<template>
  <v-app>
    <v-toolbar app flat>
      <v-toolbar-title class="headline text-uppercase">
        <span>Linda</span>
        <span class="font-weight-light"> Benchy</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn flat @click="aboutClicked">
        <span class="mr-2" >About</span>
      </v-btn>
    </v-toolbar>
    <v-content>
      <Control/>
      <v-dialog v-model="aboutDialog" scrollable max-width="300px">
      <v-card>
        <v-card-title>"Linda" WOD</v-card-title>
        <v-divider></v-divider>
        <v-card-text style="height: 300px;">
          <div>10-9-8-7-6-5-4-3-2-1 Reps, For Time</div>
          <div>&nbsp;</div>
          <h4>Advanced (Rx)</h4>
          <div>Deadlift (1½ body weight)</div>
          <div>Bench Press (body weight)</div>
          <div>Clean (¾ bodyweight)</div>
          <div>&nbsp;</div>
          <h4>Intermediate</h4>
          <div>Deadlift (1¼-body weight)</div>
          <div>Bench Press (¾ body weight)</div>
          <div>Clean (½ body weight)</div>
          <div>&nbsp;</div>
          <h4>Beginner</h4>
          <div>Deadlift (¾ body weight)</div>
          <div>Bench Press (½ body weight)</div>
          <div>Clean (⅓ body weight)</div>
          <div>&nbsp;</div>
          <hr>
          <div>&nbsp;</div>
          <div>LINDA BENCHY</div>
          <div>By: Jeff Holst</div>
          <div>Version: {{myVersion}}</div>
        </v-card-text>
        <v-divider></v-divider>
        <v-card-actions>
          <v-btn color="blue darken-1" flat @click="aboutDialog = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    </v-content>
  </v-app>
</template>

<script>
import Control from './components/Control';
import {version} from '../package.json';

export default {
  name: 'App',
  components: {
    Control,
  },
  methods: {
    aboutClicked() {
      this.aboutDialog = !this.aboutDialog;
    },
  },
  data() {
    return {
      aboutDialog: false,
      myVersion: version,
    };
  },
};
</script>
