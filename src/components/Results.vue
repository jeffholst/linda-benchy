<template>
  <v-container>
    <v-layout row>
      <v-flex>
        <h3>Time: {{timerDisplay}}</h3>
      </v-flex>
    </v-layout>
      <v-layout row>
        <v-flex xs12>
          <div>10</div>
          <div>9</div>
          <div>8</div>
          <div>7</div>
          <div>6</div>
          <div>5</div>
          <div>4</div>
          <div>3</div>
          <div>2</div>
          <div>1</div>
      </v-flex>
    </v-layout>
    <v-layout row >
      <v-flex xs12 class="text-xs-center"> 
        <v-btn
          round
          large
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
</style>
