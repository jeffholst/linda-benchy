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
          <th class="first-column"></th>
          <th>Dead</th>
          <th>Bench</th>
          <th>Clean</th>
          <th>Total</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="round in repScheme" :key="round">
          <td class="first-column">{{round}}</td>
          <td>01:02</td>
          <td>02:02</td>
          <td>03:02</td>
          <td>01:04:02</td>
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

@Component
export default class Control extends Vue {
  @Prop() private timerDisplay!: string;

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
  width: 30px;
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
