<script setup>
import * as tf from '@tensorflow/tfjs'
import * as qna from '@tensorflow-models/qna'
import 'bootstrap/dist/css/bootstrap.min.css'

import { ref } from 'vue'

defineProps({
  msg: String
})

const count = ref(0)
</script>

<script>
export default {
  name: 'QnA',
  data() {
    return {

      question: "How did Nasdaq do?",
      passage: `U.S. stocks cratered on Friday in their worst day since the throes of September's sell-off after the government's monthly employment report showed labor conditions remained tight last month despite a slowdown in hiring — dashing any hopes the Federal Reserve will pivot on its aggressive rate hiking path.

The U.S. economy added 263,000 jobs last month as the unemployment rate fell to 3.5%. Economists expected a payroll gain of 255,000 and for unemployment to hold at 3.7%, per consensus estimates compiled by Bloomberg.

The S&P 500 (^GSPC) plunged 2.8%, while the Dow Jones Industrial Average (^DJI) shed 630 points, or 2.1%. The Nasdaq Composite (^IXIC) led the way down at a decline oof 3.8%. Meanwhile in the bond market, Treasury yields spiked, with the benchmark 10-year note back near 3.9% and the rate-sensitive 2-year yield topping 4.3%.`,
      predictions: [],
      history: [],

    }
  },
  computed: {

    predText() {
      return this.predictions.map(p => p.text)
    }

  },
  methods: {
    async getPredictions() {
      console.log('getPredictions');
      console.log(this.question);
      console.log(this.passage);
      const ml = await qna.load();
      const ans = await ml.findAnswers(this.question, this.passage);
      console.log(ans);
      this.predictions = ans;
      this.history = [{ question: this.question, predictions: this.predictions }, ...this.history];
    },

  },
  mounted() {
    console.log('mounted');

  }

}
</script>
<template>
  <div class="container">
    <div class="row">
      <div class="col-7">

        <div class="form-group">
          <label for="txtPassage">Passage</label>
          <div id="divPassage" class="row"><textarea rows="10" id="txtPassage" placeholder="Passage"
              v-model="passage"></textarea>
          </div>
        </div>
        <div class="form-group">
          <label for="txtQuestion">Question</label>
          <div id="divQuestion" class="row"><input type="text" id="txtQuestion" placeholder="Question"
              v-model="question">
          </div>
        </div>
        <button class="btn btn-primary" v-on:click="getPredictions">Answer!</button>

      </div>
      <div class="col-5">
        <div v-for="h in history">
          <!-- <Transition> -->
          <div class="card" >
            <div class="card-header text-white bg-secondary">
              {{h.question}}
            </div>
            <ul class="list-group list-group-flush" v-if="h.predictions.length > 0">
              <li class="list-group-item" v-for="p in h.predictions">{{p.text}}</li>
            </ul>
            <div class="text-danger" v-if="h.predictions.length == 0">
              Answer unknown!
            </div>
          </div>
          <!-- </Transition> -->
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  /* padding: 1em; */
  margin: 0.5em;
}


</style>
