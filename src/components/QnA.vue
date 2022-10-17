
<script>
import * as tf from '@tensorflow/tfjs'
import * as qna from '@tensorflow-models/qna'
import 'bootstrap/dist/css/bootstrap.min.css'
export default {
  name: 'QnA',
  data() {
    return {
      title: "Question answering demo",
      description: `A natural language processing (NLP) project to answer comprehension questions on a
            passage.
            The project is built on TesnorFlow for javascript (TFJS) and VueJS, the TF model is BERT
            pretrained on SQUAD 2.0 dataset.`,
      ml: null,
      question: "",
      passage: "",
      predictions: [],
      history: []
    }
  },
  computed: {

    predText() {
      return this.predictions.map(p => p.text)
    }

  },
  methods: {
    async getPredictions() {
      // console.log('getPredictions');
      // console.log(this.question);
      // console.log(this.passage);

      // const ml = await qna.load();      
      // const ans = await ml.findAnswers(this.question, this.passage);
      const ans = await this.ml.findAnswers(this.question, this.passage);
      console.log(ans);
      this.predictions = ans;
      this.history = [{ question: this.question, predictions: this.predictions }, ...this.history];
    },

  },
  async mounted() {
    const ml = await qna.load();
    this.ml = Object.freeze(ml);//To prevent wrapping in proxy. 
    // console.log('mounted');
    this.passage = `U.S. stocks cratered on Friday in their worst day since the throes of September's sell-off after the government's monthly employment report showed labor conditions remained tight last month despite a slowdown in hiring — dashing any hopes the Federal Reserve will pivot on its aggressive rate hiking path.
The U.S. economy added 263,000 jobs last month as the unemployment rate fell to 3.5%. Economists expected a payroll gain of 255,000 and for unemployment to hold at 3.7%, per consensus estimates compiled by Bloomberg.
The S&P 500 (^GSPC) plunged 2.8%, while the Dow Jones Industrial Average (^DJI) shed 630 points, or 2.1%. The Nasdaq Composite (^IXIC) led the way down at a decline oof 3.8%. Meanwhile in the bond market, Treasury yields spiked, with the benchmark 10-year note back near 3.9% and the rate-sensitive 2-year yield topping 4.3%.`;
  }

}
</script>
<template>

  <main>
    <section class="py-5 text-center container">
      <div class="row py-lg-5">
        <div class="col-lg-6 col-md-8 mx-auto">
          <h1 class="fw-light">{{title}}</h1>
          <p class="lead text-muted">{{description}}</p>

        </div>
      </div>
    </section>

    <div class="py-5 bg-light">

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
              <div id="divQuestion" class="row"><input type="text" id="txtQuestion" v-model="question">
              </div>
            </div>
            <button class="btn btn-primary" v-if="ml !=null & passage != '' & question !='' "
              v-on:click="getPredictions">Answer!</button>
            <div id="divLoading" v-if="ml == null" class="spinner-border" role="status">
              <span class="sr-only">Loading...</span>
            </div>
          </div>
          <div class="col-5">
            <div v-for="h in history">
              <div class="card">
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
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
.card {
  /* padding: 1em; */
  margin: 0.5em;
}
</style>
