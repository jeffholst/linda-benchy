<template>
  <v-container grid-list-md>
    <v-layout column>
      <v-layout row>
        <v-flex xs6>
          <v-text-field v-model="weight" label="Body Weight" required></v-text-field>
        </v-flex>
        <v-flex xs6>
          <v-select
            :items="scaleItems"
            item-text="level"
            item-value="val"
            v-model="selectedScale"
            label="Scale"
          ></v-select>
        </v-flex>
      </v-layout>
      <v-layout row>
        <v-flex xs4>
          <v-card dark color="primary">
            <v-card-text>Bench</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs4>
          <v-card dark color="primary">
            <v-card-text>Deadlift</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs4>
          <v-card dark color="primary">
            <v-card-text>Clean</v-card-text>
          </v-card>
        </v-flex>
      </v-layout>
      <v-layout row>
        <v-flex text-xs-center text-xs-display-1 style="padding-top: 100px;">
          <div class="hidden-sm-and-up font-weight-black display-3">00:00:00</div>
          <div class="hidden-xs-only font-weight-black display-4">00:00:00</div>
        </v-flex>
      </v-layout>
    </v-layout>
    <v-bottom-nav :active.sync="bottomNav" :value="true" absolute color="transparent">
      <v-btn color="teal" flat value="recent" @click="startTimer();">
        <span>Start</span>
        <v-icon>timer</v-icon>
      </v-btn>

      <v-btn color="teal" flat value="favorites">
        <span>Stop</span>
        <v-icon>stop</v-icon>
      </v-btn>

      <v-btn color="teal" flat value="nearby">
        <span>Pause</span>
        <v-icon>pause_circle_outline</v-icon>
      </v-btn>
    </v-bottom-nav>
  </v-container>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

enum TimerStatus {
  Stopped = 0,
  Running = 1,
  Paused = 2,
}

@Component
export default class Control extends Vue {
  public bottomNav: string = '';
  public weight: string = '180';
  public selectedScale: string = '1';
  public scaleItems: any = [
    { level: 'Advanced (Rx)', val: '1' },
    { level: 'Intermediate', val: '2' },
    { level: 'Beginner', val: '3' },
  ];
  public timerStatus: TimerStatus = TimerStatus.Stopped;

  constructor() {
    super();
  }

  public startTimer() {
    // tslint:disable-next-line:no-console
    console.log('Start timer clicked');
  }
}
</script>

<style>
</style>
