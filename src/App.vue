<template>
    <div id="app">
        <div class="container-fluid container-main">
            <div class="row">
                <div class="col-md-12">
                    <h1>The Current Question!</h1>
                    <div class="container">
                        <p>{{ singleQuestion }}</p>
                    </div>
                    <button class="btn btn-primary" @click="nextQuestion()">Generate New Question</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    'data' () {
        return {
            playerOne: false,
            playerTwo: false,
            currentQuestion: 0,
            questions: [],
            singleQuestion: ''
        }
    },
    'events': {
        'hook:ready': function () {
            this.$http.get('../static/questions.json').then((response) => {
                var questions = response.data
                var currentIndex = questions.length
                var temporaryValue
                var randomIndex
                // var singleQuestion
                while (currentIndex !== 0) {
                    // singleQuestion = questions[i].body
                    randomIndex = Math.floor(Math.random() * currentIndex)
                    currentIndex -= 1
                    temporaryValue = questions[currentIndex]
                    questions[currentIndex] = questions[randomIndex]
                    questions[randomIndex] = temporaryValue
                }
                this.$set('questions', questions)
            })
        }
    },
    'methods': {
        'nextQuestion': function () {
            this.singleQuestion = this.questions.shift().body
        }
    }
}
</script>

<style lang="scss">

html {
    height: 100%;
    font-size: 16px;
}

body {
    background-color: #42413d;
    color: #b2afac;
    font-size: 62.5%;
}

h1 {
    font-size: 2.5rem;
}

p {
    font-size: 1rem;
    color: white;
}

.container-main {
    margin-top: 20%;
    text-align: center;
}

.container {
    margin: auto;
    width: 30rem;
    padding: 15px;
}

.btn-primary {
    background-color: #ffce2b;
    color: white;
    border: 1px solid #e6b000;
}

.btn-primary:hover,
.btn-pimary:active,
.btn-primary:focus,
.btn-primary:visited,
.btn-primary:link {
    background-color: #b38900;
    color: white;
    border: 1px solid #e6b000;
}

</style>
