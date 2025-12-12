<template>

  <div>


    <ScoreQuiz :winCount="this.winCount" :loseCount="this.loseCount" />

    <template v-if="question">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer, index) in this.answers" :key="index">

        <input :disabled="this.answerSubmitted" type="radio" name="options" :value="answer"
          v-model="this.chosenAnswer" />
        <label v-html="answer"></label><br />
      </template>

      <button v-if="!this.answerSubmitted" @click="this.submitAnswer()" class="send" type="button">Send</button>

      <section class="result" v-if="this.answerSubmitted">
        <template v-if="this.chosenAnswer == this.correctAnswer">
          <h4 v-html="'&#9989; Congrulations, the answer ' + this.correctAnswer + ' is correct.'">

          </h4>
        </template>

        <template v-else>
          <h4 v-html="'&#10060; I´m sorry, you picked the wrong answer. The correct is' + this.correctAnswer + '.'">
          </h4>
        </template>

        <button @click="this.getNewQuestion()" class="send" type="button">Next question</button>
      </section>

    </template>

  </div>

</template>

<script>
import ScoreQuiz from './components/ScoreQuiz.vue';


export default {
  name: 'App',
  components: {
    ScoreQuiz
  },

  data() {
    return {
      question: "",
      incorrectAnswers: [],
      correctAnswer: [],
      chosenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
    }
  },

  computed: {
    answers() {
      var answers = [...this.incorrectAnswers];
      // eslint-disable-next-line no-unused-vars
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },

  methods: {
    submitAnswer() {
      if (!this.chosenAnswer) {
        console.log("Pick one of the options");
      } else {
        this.answerSubmitted = true
        if (this.chosenAnswer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },

    getNewQuestion() {

      this.answerSubmitted = false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      this.axios.get('https://opentdb.com/api.php?amount=1&category=18').then((response) => {
        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
      })
    }

  },

  created() {
    this.getNewQuestion();
  }

}

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type='radio'] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>