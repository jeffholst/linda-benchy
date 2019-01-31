<template>
  <v-app>
    <v-toolbar app flat>
      <v-toolbar-title class="headline text-uppercase">
        <span>Linda</span>
        <span class="font-weight-light">Benchy</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn flat @click="aboutClicked">
        <span class="mr-2">About</span>
      </v-btn>
      <v-tabs slot="extension" v-model="model" centered slider-color="black">
        <v-tab href="#tab1">Setup</v-tab>
        <v-tab href="#tab2">Workout</v-tab>
        <v-tab href="#tab3">Results</v-tab>
      </v-tabs>
    </v-toolbar>
    <v-content>
      <v-tabs-items v-model="model">
        <v-tab-item value="tab1">
          <v-card flat>
            <Setup/>
          </v-card>
        </v-tab-item>
        <v-tab-item value="tab2">
          <v-card flat>
            <Workout @start-timer="timerStarted" @stop-timer="timerStopped"/>
          </v-card>
        </v-tab-item>
        <v-tab-item value="tab3">
          <v-card flat>
            <Results/>
          </v-card>
        </v-tab-item>
      </v-tabs-items>

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
import Setup from './components/Setup';
import Workout from './components/Workout';
import Results from './components/Results';
import { version } from '../package.json';
import NoSleep from 'nosleep.js';

const noSleep = new NoSleep();

export default {
  name: 'App',
  components: {
    Setup,
    Workout,
    Results,
  },
  methods: {
    aboutClicked() {
      this.aboutDialog = !this.aboutDialog;
    },
    timerStarted(event, value) {
      document.addEventListener(
        'click',
        function enableNoSleep() {
          document.removeEventListener('click', enableNoSleep, false);
          noSleep.enable();
        },
        false,
      );
    },
    timerStopped(event, value) {
      noSleep.disable();
    },
  },
  data() {
    return {
      model: 'tab1',
      aboutDialog: false,
      myVersion: version,
    };
  },
};
</script>
