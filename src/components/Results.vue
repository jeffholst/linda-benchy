<template>
  <v-container>
    <v-layout row>
      <v-flex>
        <h3>Time: {{timerDisplay}}</h3>
      </v-flex>
    </v-layout>
    <table class="result-table tiny-table">
      <thead>
        <tr>
          <th class="first-column">Reps</th>
          <th>Dead</th>
          <th>Bench</th>
          <th>Clean</th>
          <th>Total</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="round in repScheme" :key="round">
          <td class="first-column">{{round | padZero}}</td>
          <td>{{deadTimeAry[round -1] | formatLift}}</td>
          <td>{{benchTimeAry[round -1] | formatLift}}</td>
          <td>{{cleanTimeAry[round -1] | formatLift}}</td>
          <td>{{totalTimeAry[round -1] | formatTotal}}</td>
        </tr>
      </tbody>
    </table>
    <v-layout row style="padding-top: 40px;">
      <v-flex xs12 class="text-xs-center"> 
        <v-btn
          round
          color="orange"
          class="white--text"
          @click="resetApp"
          >Reset
        </v-btn>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component({
  filters: {
    padZero(value: number) {
      return ('0' + value).slice(-2);
    },
    formatLift(value: any) {
      const seconds: number = value % 60;
      const minutes: number = Math.trunc((value / 60)) % 60;

      const minutesPadded = ('0' + minutes).slice(-2);
      const secondsPadded = ('0' + seconds).slice(-2);
      
      return(`${minutesPadded}:${secondsPadded}`);
    },
    formatTotal(value: any) {
      const seconds: number = value % 60;
      const minutes: number = Math.trunc((value / 60)) % 60;
      const hours: number = Math.trunc((value / 60 / 60)) % 60;

      const hoursPadded = ('0' + hours).slice(-2);
      const minutesPadded = ('0' + minutes).slice(-2);
      const secondsPadded = ('0' + seconds).slice(-2);
      
      return(`${hoursPadded}:${minutesPadded}:${secondsPadded}`);
    },
  },
})
export default class Control extends Vue {
  @Prop() private timerDisplay!: string;
  @Prop() private deadTimeAry!: any;
  @Prop() private benchTimeAry!: any;
  @Prop() private cleanTimeAry!: any;
  @Prop() private totalTimeAry!: any;

  private repScheme: number[] = [10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ];

  private resetApp() {
    this.$emit('reset-app');
  }
}
</script>

<style>
.result-table {
    border-collapse: collapse;
    border-spacing: 0;
    width: 100%;
    display: table
}

.result-table tr:nth-child(odd) {
    background-color: #fff
}

.result-table tr:nth-child(even) {
    background-color: #f1f1f1
}

.result-table th {
    padding: 4px 0px 4px 0px;
    display: table-cell;
    text-align: left;
    vertical-align: top;
    font-weight: 800;
}

.first-column {
  width: 40px;
}

.tiny-table {
    font-size: 10px !important
}

.small-table {
    font-size: 12px !important
}

.medium-table {
    font-size: 15px !important
}

.large-table {
    font-size: 18px !important
}
</style>
