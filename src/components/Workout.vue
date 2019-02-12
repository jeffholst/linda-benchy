<template>
  <v-container grid-list-md>
    <v-layout row>
      <v-flex text-xs-center headline style="padding-top: 0px;">{{startingReps - roundsComplete}} REPS EACH</v-flex>
    </v-layout>
    <v-layout row text-xs-center>
      <v-flex xs4>
        <div>Dead</div>
        <div>{{deadWeight}} {{weightMeasurementLable}}</div>
        <v-btn 
          fab 
          color="blue" 
          class="white--text"
          @click="finishDead();"
          :disabled="disableDeadButton"
          >
          <v-icon>done_outline</v-icon>
        </v-btn>
      </v-flex>
      <v-flex xs4>
        <div>Bench</div>
        <div>{{benchWeight}} {{weightMeasurementLable}}</div>
        <v-btn 
          fab color="blue" 
          class="white--text"
          @click="finishBench();"
          :disabled="disableBenchButton"
          >
          <v-icon>done_outline</v-icon>
        </v-btn>
      </v-flex>
      <v-flex xs4>
        <div>Clean</div>
        <div>{{cleanWeight}} {{weightMeasurementLable}}</div>
        <v-btn 
          fab 
          color="blue" 
          class="white--text"
          @click="finishClean();"
          :disabled="disableCleanButton"
          >
          <v-icon>done_outline</v-icon>
        </v-btn>
      </v-flex>
    </v-layout>
    <v-layout row >
      <v-flex text-xs-center header class="orange--text">
        <div v-if="showClickTip">Click <v-icon small class="orange--text">done_outline</v-icon> when set complete</div>
        <div v-else>&nbsp;</div>
      </v-flex>
    </v-layout>
    <v-layout row>
      <v-flex text-xs-center text-xs-display-1>
        <div class="hidden-sm-and-up font-weight-black display-2">{{timerDisplay}}</div>
        <div class="hidden-xs-only font-weight-black display-4">{{timerDisplay}}</div>
      </v-flex>
    </v-layout>
    <v-layout row>
      <v-flex text-xs-center text-xs-display-1 style="padding-top: 10px;">
        <v-btn
          v-if="!disableStartButton"
          small
          round
          color="green"
          class="white--text"
          @click="startTimer();"
        >
          {{startButtonText}}&nbsp;
          <v-icon>timer</v-icon>
        </v-btn>
        <v-btn
          v-if="!disableStopButton"
          small
          round
          color="red"
          class="white--text"
          @click="stopTimer();"
        >Stop&nbsp;
          <v-icon>stop</v-icon>
        </v-btn>
        <v-btn
          v-if="!disablePauseButton"
          small
          round
          color="blue"
          class="white--text"
          @click="pauseTimer();"
        >Pause&nbsp;
          <v-icon>pause_circle_outline</v-icon>
        </v-btn>
      </v-flex>
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
  @Prop() private selectedScale!: string;
  @Prop() private startingReps!: number;
  @Prop() private deadWeight!: number;
  @Prop() private benchWeight!: number;
  @Prop() private cleanWeight!: number;
  @Prop() private weightMeasurementLable!: string;

  private buttonDeadClicked: boolean = false;
  private buttonBenchClicked: boolean = false;
  private buttonCleanClicked: boolean = false;
  private currentReps: number = this.startingReps;
  private disablePauseButton: boolean = true; // indicates if pause button should be displayed
  private disableStartButton: boolean = false; // indicates if start button should be displayed
  private disableStopButton: boolean = true; // indicates if stop button should be dispalyed
  private showClickTip: boolean = false;
  private startButtonText: string = 'START'; // text displayed on the start/continue button
  private roundsComplete: number = 0;
  private timer: number = 0; // setInterval timer
  private timerDisplay: string = '00:00:00'; // the timer display the user sees
  private timerPausedTime: number = 0; // time timer has been paused in seconds
  private timerStartTime: number = 0; // timer start time, millisecs since JAN-01-1970
  private timerStatus: TimerStatus = TimerStatus.Stopped; // status of the timer
  private totalSeconds: number = 0; // total seconds timer has been going without pauses

  private get disableDeadButton() {
    const rval: boolean = this.buttonDeadClicked || this.timerStatus !== TimerStatus.Running;
    return rval;
  }

  private get disableBenchButton() {
    const rval: boolean = this.buttonBenchClicked || this.timerStatus !== TimerStatus.Running;
    return rval;
  }

  private get disableCleanButton() {
    const rval: boolean = this.buttonCleanClicked || this.timerStatus !== TimerStatus.Running;
    return rval;
  }


  private updateTimer() {
    /*
      called every second from timer setInterval while timer is running or paused
    */

    if (this.timerStatus === TimerStatus.Running) {
      // update timer display
      // this.timerDisplay = `${this.GetHours()}:${this.GetMinutes()}:${this.GetSeconds()}`;
      this.timerDisplay = this.UpdateTimerDisplay();
      // stop the timer if it's been running this long
      if (this.totalSeconds === 359999) {
        // 99:59:59
        this.stopTimer();
      }
    } else {
      this.timerPausedTime++;
    }
  }

  private UpdateTimerDisplay() {
    /*
      return timer display and update totalSeconds
    */
    const d: Date = new Date();
    this.totalSeconds =
      Math.floor((d.getTime() - this.timerStartTime) / 1000) -
      this.timerPausedTime;
    return `${this.GetHours()}:${this.GetMinutes()}:${this.GetSeconds()}`;
  }

  private GetSeconds() {
    /*
      return seconds portion 00:00:ss
    */
    const sec: number = this.totalSeconds % 60;
    return sec >= 10 ? sec : '0' + sec;
  }

  private GetMinutes() {
    /*
      return minutes portion 00:mm:00
    */
    const min: number = Math.floor((this.totalSeconds / 60) % 60);
    return min >= 10 ? min : '0' + min;
  }

  private GetHours() {
    /*
      return hours portion hh:00:00
    */
    const hrs: number = Math.floor(this.totalSeconds / 60 / 60);
    return hrs >= 10 ? hrs : '0' + hrs;
  }

  private startTimer() {
    /*
      start timer
    */

    if (this.timerStatus !== TimerStatus.Paused) {
      this.totalSeconds = 0;
      this.timer = setInterval(this.updateTimer, 1000);
      // start noSleep operation, keeps PWAs from going to sleep while timer is going
      this.$emit('start-timer');
      const d = new Date();
      this.timerStartTime = d.getTime(); // get the current number of milliseconds since Jan 1 1970
      this.showClickTip = true;
    }
    this.timerStatus = TimerStatus.Running;
    this.disableStartButton = true;
    this.disableStopButton = false;
    this.disablePauseButton = false;
  }

  private stopTimer() {
    /*
      stop timer
    */

    clearInterval(this.timer);
    this.startButtonText = 'START';
    this.timerStatus = TimerStatus.Stopped;
    this.disableStartButton = false;
    this.disableStopButton = true;
    this.disablePauseButton = true;
    this.timerPausedTime = 0;
    this.buttonDeadClicked = false;
    this.buttonBenchClicked = false;
    this.buttonCleanClicked = false;
    this.roundsComplete = 0;
    // cancel noSleep operation
    this.$emit('stop-timer');
    this.workoutComplete();
    this.timerDisplay = '00:00:00';
    this.showClickTip = false;
  }

  private pauseTimer() {
    /*
      pause timer
    */

    this.timerStatus = TimerStatus.Paused;
    this.startButtonText = 'CONTINUE';
    this.disableStartButton = false;
    this.disableStopButton = false;
    this.disablePauseButton = true;
  }

  private workoutComplete() {
    /*
      workout finished
    */
    this.$emit('workout-complete', this.timerDisplay); // finished workout
  }

  private finishDead() {
    this.buttonDeadClicked = true;
    this.$emit('lift-complete', 1, this.startingReps - this.roundsComplete, this.totalSeconds);
    this.checkForRoundComplete();
  }

  private finishBench() {
    this.buttonBenchClicked = true;
    this.$emit('lift-complete', 2, this.startingReps - this.roundsComplete, this.totalSeconds);
    this.checkForRoundComplete();
  }

  private finishClean() {
    this.buttonCleanClicked = true;
    this.$emit('lift-complete', 3, this.startingReps - this.roundsComplete, this.totalSeconds);
    this.checkForRoundComplete();
  }

  private checkForRoundComplete() {
    this.showClickTip = false;
    if ( this.buttonDeadClicked && this.buttonBenchClicked && this.buttonCleanClicked ) {
      this.buttonDeadClicked = false;
      this.buttonBenchClicked = false;
      this.buttonCleanClicked = false;
      this.roundsComplete++;

      if ( this.roundsComplete === this.startingReps ) {
        this.stopTimer();
      }
    }
  }
}
</script>

<style>
</style>
