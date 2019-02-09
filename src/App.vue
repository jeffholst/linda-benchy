<template>
  <v-app>
    <v-toolbar app flat>
      <v-toolbar-title class="headline text-uppercase">
        <span>Linda</span>
        <span class="font-weight-light"> WOD</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn flat @click="aboutClicked">
        <span class="mr-2">Help</span>
      </v-btn>
    </v-toolbar>
    <v-content>
      <v-container fluid fill-height>
        <v-layout>
          <v-flex>
            <v-stepper v-model="stepCompleted" style="height: 100%">
              <v-stepper-header>
                <v-stepper-step :complete="stepCompleted > 1" step="1">Setup</v-stepper-step>

                <v-divider></v-divider>

                <v-stepper-step :complete="stepCompleted > 2" step="2">Workout</v-stepper-step>

                <v-divider></v-divider>

                <v-stepper-step :complete="stepCompleted > 2" step="3">Results</v-stepper-step>
              </v-stepper-header>

              <v-stepper-items>
                <v-stepper-content step="1">
                  <Setup 
                    @scale-changed="selectedScaleChanged"
                    @setup-complete="setupComplete"
                  />
                </v-stepper-content>

                <v-stepper-content step="2">
                  <Workout 
                    @start-timer="timerStarted" 
                    @stop-timer="timerStopped"
                    @workout-complete="workoutComplete"
                    v-bind:selectedScale="selectedScale" 
                    v-bind:startingReps="startingReps" 
                    v-bind:deadWeight="deadWeight"
                    v-bind:benchWeight="benchWeight"
                    v-bind:cleanWeight="cleanWeight"
                  />
                </v-stepper-content>

                <v-stepper-content step="3">
                  <Results 
                    @reset-app="resetApp"
                    v-bind:timerDisplay="timerDisplay"
                  />
                </v-stepper-content>
              </v-stepper-items>
            </v-stepper>
            <v-dialog v-model="aboutDialog" scrollable max-width="300px">
              <v-card>
                <v-card-title class="text-uppercase">Linda WOD</v-card-title>
                <v-divider></v-divider>
                <v-card-text style="height: 300px;">
                  <p>
                    <strong>Reference: </strong><a href= "https://www.crossfit.com/workout/2018/05/26#/">CrossFit Linda</a>
                  </p>
                  <p>10-9-8-7-6-5-4-3-2-1 Reps, For Time<br>
                  Deadlift (1½ body weight)<br>
                  Bench Press (body weight)<br>
                  Clean (¾ bodyweight)<br>
                  </p>
                  <p>Set up three bars and storm through for time.</p>
                  <p>See below for scaling options.</p>
                  <v-divider/>
                  <div>&nbsp;</div>
                  <h3>Scaling</h3>
                  <div>&nbsp;</div>
                  <h4>Intermediate</h4>
                  <div>10-9-8-7-6-5-4-3-2-1 reps for time of:</div>
                  <div>Deadlift (1¼-body weight)</div>
                  <div>Bench Press (¾ body weight)</div>
                  <div>Clean (½ body weight)</div>
                  <div>&nbsp;</div>
                  <h4>Beginner</h4>
                  <div>8-7-6-5-4-3-2-1 reps for time of:</div>
                  <div>Deadlift (¾ body weight)</div>
                  <div>Bench Press (½ body weight)</div>
                  <div>Clean (⅓ body weight)</div>
                  <div>&nbsp;</div>
                  <v-divider/>
                  <div>&nbsp;</div>
                  <div>LINDA WOD</div>
                  <div>By: Jeff Holst</div>
                  <div>Version: {{myVersion}}</div>
                </v-card-text>
                <v-divider></v-divider>
                <v-card-actions>
                  <v-btn color="blue darken-1" flat @click="aboutDialog = false">Close</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-flex>
        </v-layout>
      </v-container>
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
    selectedScaleChanged(newValue) {
      this.selectedScale = newValue;
      if ( this.selectedScale === '1' || this.selectedScale === '2' ) {
        this.startingReps = 10;
      }
      else {
        this.startingReps = 8;
      }
    },
    setupComplete(deadWeight, benchWeight, cleanWeight) {
      this.stepCompleted = 2;
      this.deadWeight = deadWeight;
      this.benchWeight = benchWeight;
      this.cleanWeight = cleanWeight;
    },
    workoutComplete(workoutTime) {
      this.stepCompleted = 3;
      this.timerDisplay = workoutTime;
    },
    resetApp() {
      this.stepCompleted = 1;
    },
  },
  data() {
    return {
      startingReps: 10,
      stepCompleted: 0,
      aboutDialog: false,
      myVersion: version,
      selectedScale: '1',   // 1=Rx, 2=Intermediate, 3=Beginner
      timerDisplay: '',
      deadWeight: 0,
      benchWeight: 0,
      cleanWeight: 0,
    };
  },
};
</script>
