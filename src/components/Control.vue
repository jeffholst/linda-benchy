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
            <v-card-title primary-title class="justify-center" >
              <div >
                <h4>BENCH</h4>
                <h3>100lbs</h3>
              </div>
            </v-card-title>
          </v-card>
        </v-flex>
        <v-flex xs4>
          <v-card dark color="primary"> 
            <v-card-title primary-title class="justify-center" >
              <div >
                <h4>DEADLIFT</h4>
                <h3>100lbs</h3>
              </div>
            </v-card-title>
          </v-card>
        </v-flex>
        <v-flex xs4>
          <v-card dark color="primary"> 
            <v-card-title primary-title class="justify-center" >
              <div >
                <h4>CLEAN</h4>
                <h3>100lbs</h3>
              </div>
            </v-card-title>
          </v-card>
        </v-flex>
      </v-layout>
      <v-layout row>
        <v-flex text-xs-center text-xs-display-1 style="padding-top: 60px;">
          <div class="hidden-sm-and-up font-weight-black display-3">{{timerDisplay}}</div>
          <div class="hidden-xs-only font-weight-black display-4">{{timerDisplay}}</div>
        </v-flex>
      </v-layout>
      <v-layout row>
        <v-flex text-xs-center text-xs-display-1 style="padding-top: 10px;">
          <v-btn
            v-if="!disableStartButton"
            round
            large
            color="green"
            class="white--text"
            @click="startTimer();"
          >{{startButtonText}}&nbsp;
            <v-icon>timer</v-icon>
          </v-btn>
          <v-btn
            v-if="!disableStopButton"
            round
            large
            color="red"
            class="white--text"
            @click="stopTimer();"
          >Stop&nbsp;
            <v-icon>stop</v-icon>
          </v-btn>
          <v-btn
            v-if="!disablePauseButton"
            round
            large
            color="blue"
            class="white--text"
            @click="pauseTimer();"
          >Pause&nbsp;
            <v-icon>pause_circle_outline</v-icon>
          </v-btn>
        </v-flex>
      </v-layout>
    </v-layout>
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
  private startButtonText: string = 'START';
  private timer: number = 0;
  private totalSeconds: number = 0;
  private timerDisplay: string = '00:00:00';
  private disableStartButton: boolean = false;
  private disableStopButton: boolean = true;
  private disablePauseButton: boolean = true;
  private bottomNav: string = '';
  private weight: string = '180';
  private selectedScale: string = '1';
  private scaleItems: any = [
    { level: 'Advanced (Rx)', val: '1' },
    { level: 'Intermediate', val: '2' },
    { level: 'Beginner', val: '3' },
  ];
  private timerStatus: TimerStatus = TimerStatus.Stopped;

  constructor() {
    super();
  }

  public updateTimer() {
    if (this.timerStatus === TimerStatus.Running) {
      this.totalSeconds++;
      this.timerDisplay = `${this.GetHours()}:${this.GetMinutes()}:${this.GetSeconds()}`;

      if (this.totalSeconds === 359999) {
        // 99:59:59
        this.stopTimer();
      }
    }
  }

  public GetSeconds() {
    const sec: number = this.totalSeconds % 60;
    return sec >= 10 ? sec : '0' + sec;
  }

  public GetMinutes() {
    const min: number = Math.floor((this.totalSeconds / 60) % 60);
    return min >= 10 ? min : '0' + min;
  }

  public GetHours() {
    const hrs: number = Math.floor(this.totalSeconds / 60 / 60);
    return hrs >= 10 ? hrs : '0' + hrs;
  }

  public startTimer() {
    if (this.timerStatus !== TimerStatus.Paused) {
      this.totalSeconds = 0;
      this.timer = setInterval(this.updateTimer, 1000);
    }
    this.timerStatus = TimerStatus.Running;
    this.disableStartButton = true;
    this.disableStopButton = false;
    this.disablePauseButton = false;
  }

  public stopTimer() {
    clearInterval(this.timer);
    this.startButtonText = 'START';
    this.timerStatus = TimerStatus.Stopped;
    this.disableStartButton = false;
    this.disableStopButton = true;
    this.disablePauseButton = true;
  }

  public pauseTimer() {
    this.timerStatus = TimerStatus.Paused;
    this.startButtonText = 'CONTINUE';
    this.disableStartButton = false;
    this.disableStopButton = false;
    this.disablePauseButton = true;
  }
}
</script>

<style>
</style>
