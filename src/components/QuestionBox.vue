<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>{{ currentQuestion.question }}</template>

      <hr class="my-4" />
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click="selectAnswer(index)"
          :class="[answerClass(index)]"
        >{{answer}}</b-list-group-item>
      </b-list-group>
      <!-- :key must be a unique identifier -->

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
      >Submit</b-button>
      <b-button @click="next" variant="success" href="#">Next Question</b-button>
    </b-jumbotron>
  </div>
</template>  

<script>
// lodash is javascript utility library
import _ from "lodash";
export default {
  // whatever variables / methods we are receiving from parent must be here so they can be rendered
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
  // watch for changes to props and run function if changed
  watch: {
    currentQuestion: {
      // watching currentQuestion.
      // instead of only running watch function when changes, run initially also when currentQuestion gets passed as props
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    submitAnswer() {
      this.increment(this.selectedIndex === this.correctIndex);
      this.answered = true;
    },
    answerClass(index) {
      let answerClass = ''

      if (!this.answered && this.selectedIndex === index) { 
        answerClass = "selected"
      } else if (this.answered && this.correctIndex === index) {
        answerClass = "correct"
      } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        answerClass = "incorrect"
      }
      return answerClass      
    },
  },
};
</script>

// scope = not global style. Only in this component
<style scoped>
.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background: #eee;
  cursor: pointer;
}

.btn {
  margin: 0 5px;
}

.selected {
  background: lightblue;
}

.correct {
  background: lightgreen;
}

.incorrect {
  background: red;
}
</style>