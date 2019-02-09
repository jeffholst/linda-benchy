<template>
  <v-container grid-list-md>
    <v-layout row>
      <v-flex text-xs-center text-xs-display-1 style="padding-top: 0px;">Round 1 of 10</v-flex>
    </v-layout>
    <v-layout row text-xs-center>
      <v-flex xs4>
        <v-btn fab small color="blue" class="hidden-sm-and-up white--text">
          <v-icon>done_outline</v-icon>
        </v-btn>
        <v-btn large color="blue" class="hidden-xs-only white--text">{{buttonNameDeadlift}}</v-btn>
      </v-flex>
      <v-flex xs4>
        <v-btn fab small color="blue" class="hidden-sm-and-up white--text">
          <v-icon>done_outline</v-icon>
        </v-btn>
        <v-btn large color="blue" class="hidden-xs-only white--text">{{buttonNameBenchpress}}</v-btn>
      </v-flex>
      <v-flex xs4>
        <v-btn fab small color="blue" class="hidden-sm-and-up white--text">
          <v-icon>done_outline</v-icon>
        </v-btn>
        <v-btn large color="blue" class="hidden-xs-only white--text">{{buttonNameClean}}</v-btn>
      </v-flex>
    </v-layout>
    <v-layout row>
      <v-flex text-xs-center text-xs-display-1 style="padding-top: 20px;">
        <div class="hidden-sm-and-up font-weight-black display-3">{{timerDisplay}}</div>
        <div class="hidden-xs-only font-weight-black display-4">{{timerDisplay}}</div>
      </v-flex>
    </v-layout>
    <v-layout row>
      <v-flex text-xs-center text-xs-display-1 style="padding-top: 30px;">
        <v-btn
          v-if="!disableStartButton"
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
          round
          color="red"
          class="white--text"
          @click="stopTimer();"
        >Stop&nbsp;
          <v-icon>stop</v-icon>
        </v-btn>
        <v-btn
          v-if="!disablePauseButton"
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

  private buttonNameDeadlift: string = 'Deadlift'; // display name for deadlift button
  private buttonNameBenchpress: string = 'Bench'; // display name for bench press button
  private buttonNameClean: string = 'Clean'; // display name for clean button
  private disablePauseButton: boolean = true; // indicates if pause button should be displayed
  private disableStartButton: boolean = false; // indicates if start button should be displayed
  private disableStopButton: boolean = true; // indicates if stop button should be dispalyed
  private startButtonText: string = 'START'; // text displayed on the start/continue button
  private timer: number = 0; // setInterval timer
  private timerDisplay: string = '00:00:00'; // the timer display the user sees
  private timerPausedTime: number = 0; // time timer has been paused in seconds
  private timerStartTime: number = 0; // timer start time, millisecs since JAN-01-1970
  private timerStatus: TimerStatus = TimerStatus.Stopped; // status of the timer
  private totalSeconds: number = 0; // total seconds timer has been going without pauses

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
    // cancel noSleep operation
    this.$emit('stop-timer');
    this.workoutComplete();
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
}
</script>

<style>
</style>
