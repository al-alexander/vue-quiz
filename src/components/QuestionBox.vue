<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item 
          v-for="(answer, index) in answers"
          :key="index" 
          @click="selectAnswerFunc(index)" 
          :class="answerClass(index)"
          >
        {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button 
        variant="primary" 
        href="#" 
        v-on:click="submitAnswerFunc"
        :disabled="selectedIndex === null || answered"
        >Submit</b-button>
      <b-button @click="nextFunc"  variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    nextFunc: Function,
    incrementFunc: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false

    }
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      return answers
    }
  },
  mounted() {
    //this.shuffledAnswers()
  },
  methods: {
    selectAnswerFunc(index) {
      this.selectedIndex = index
      console.log(this.selectedIndex)
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    submitAnswerFunc() {
      let isCorrect = false
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }
      this.answered = true
      this.incrementFunc(isCorrect)
    },
    answerClass(index) {
      let answerClass = 'answered'
      
      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      }
      else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      }
      else if (this.answered && this.correctIndex !== this.selectedIndex && this.selectedIndex == index) {
        answerClass = 'incorrect'
      }
      return answerClass
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
      }
    }
    /* currentQuestion() {
      this.selectedIndex = null
      this.shuffleAnswers() */
    
  }
}
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background: #EEE;
  cursor: pointer;
}

.selected {
  background-color: lightblue;
}

.btn {
  margin: 0 5px;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: red;
}

</style>
