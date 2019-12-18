<template>
    <div class="question-box-container">
        
    <b-jumbotron>
        <template v-slot:lead>
            {{ currentQuestion.question}}
        </template>

        <hr>

        <b-list-group 
            v-for="(answer, index) in answers" v-bind:key="index" 
            @click.prevent="selectAnswer(index)" > 

            <b-list-group-item 
                button true
                v-bind:class="answerClass(index)" >
                {{ answer }}
            </b-list-group-item>
        </b-list-group>

        <b-button variant="primary"
            @click="submitAnswer"
            :disabled="selectedIndex === null || answered"> Submit </b-button>
        <b-button variant="success" @click="next"> Go next </b-button>
    </b-jumbotron>

    </div>
</template>


<script>
import _ from 'lodash'

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function
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
    watch: {
        currentQuestion() {
            this.selectedIndex = null
            this.answered = false
            this.shuffleAnswers()
        }
    },
    methods: {
        selectAnswer(index) {
            this.selectedIndex = index
        
            },
        shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
            },
        submitAnswer() {
            let isCorrect = false
                if (this.selectedIndex === this.correctIndex) {
                    isCorrect = true
            }
            this.answered = true
            this.increment(isCorrect)
        },
        answerClass(index ) {
            let answerClass = ''
            
            if (!this.answered && this.selectedIndex === index) {
                answerClass = 'selected'
            } else if (this.answered && this.correctIndex === index) {
                answerClass = 'correct'
            } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                answerClass = 'incorrect'
            }
             return answerClass
        }

    },
    mounted() {
        this.shuffleAnswers()
    }
}
</script>


<style scoped> 
.list-group {
    margin-bottom: 8px
}

.list-group-item:hover {
    background: rgb(203, 211, 255)
}
.btn {
    margin: 0 5px
}

.selected {
    background-color:rgb(143, 150, 255)
}

.correct {
    background-color: rgb(132, 245, 109)
}

.incorrect {
    background-color: rgb(247, 100, 100)
}
</style>