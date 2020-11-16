<template>
    <div class="question-box-container">
        <b-jumbotron >
            
            <template #lead>
                {{question.question}}
            </template>

            <hr class="my-4">

            <b-list-group >
                <b-list-group-item  v-for="(answer,index) in shuffledAnswers" :key="answer" @click="selectAnswer(index)" :class="answeredClass(index)">{{answer}}</b-list-group-item>
            </b-list-group>


            <b-button variant="primary" :disabled="selectedIndex=== null || answered"  @click="submitAnswer">Submit</b-button>
            <b-button variant="success" @click="next">Next</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'
export default {
    props : {
        question : Object,
        next : Function,
        increment : Function
    },
    methods:{
        selectAnswer(index) {this.selectedIndex = index},
        shuffleAnswers(){
            let answers = [...this.question.incorrect_answers, this.question.correct_answer]
            this.shuffledAnswers = _.shuffle(answers).sort()
            this.correctIndex = this.shuffledAnswers.indexOf(this.question.correct_answer)
        },
        submitAnswer(){
            let isCorrect = false;

            if(this.correctIndex=== this.selectedIndex){isCorrect = true;}

            this.answered = true
            this.increment(isCorrect)
        },
        answeredClass(index){
            let answerClass = ''

            if(!this.answered && this.selectedIndex===index){
                answerClass = 'selected'
            }else if(this.answered && this.correctIndex===index){
                answerClass = 'correct'
            }else if(this.answered && this.selectedIndex===index && this.correctIndex!==index){
                answerClass='incorrect'
            }
                return answerClass
        }
    },
    data(){
        return {
            selectedIndex : null,
            shuffledAnswers: [],
            correctIndex : null,
            answered : false
        }
    },
    computed:{
        answers() {
            let answers = [...this.question.incorrect_answers]
            answers.push(this.question.correct_answer)
            //answers.sort()
            return(answers)
        }
    },
    watch:{
        question: {
            immediate:true,
            handler(){
            this.selectedIndex = null;
            this.answered = false;
            this.shuffleAnswers();
            }
        }
    }
}
</script>

<style scoped>
    .list-group {
        margin-bottom: 15px;
    }
    .list-group-item:hover{
        background-color: #EEE;
        cursor: pointer;
    }
    .btn{
        margin: 0 5px;
    }
    .selected{
        background-color: rgb(81, 192, 236);
    }
    .correct{
        background-color: green;
    }
    .incorrect{
        background-color: red;
    }
</style>