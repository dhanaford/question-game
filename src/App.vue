<template>
    <div id="app">
        <div v-if="state.showSplash" class="splash-screen">
          <div class="instruction-dialog">
            <h2>Welcome to the Random Question Generator!</h2>
            <p>This game is meant to inspire conversation and can also be a great way to break the ice. This tool can be used solo, i.e (if you are on a date and need a quick way to start conversation), or played with any number of people. There are 2 basic rules.</p>
            <ol>
              <li>One person asks and generates the questions</li>
              <li>Each person answers but may feel free to just pass if they don’t feel like answering</li>
            </ol>
            <p>As long as each person has their own time to have a turn to give there answer without interruption.</p>
            <form class="form-inline category">
              <p>Pick a category below to get started:</p>
              <input v-model="state.chosenCategory" value="silly" class="form-control" type="radio"></input>
              <label>Silly</label>
              <input v-model="state.chosenCategory" value="deep" class="form-control" type="radio"></input>
              <label>Deep</label>
              <input v-model="state.chosenCategory" value="hypothetical" class="form-control" type="radio"></input>
              <label>Hypothetical</label>
              <input v-model="state.chosenCategory" value="absurd" class="form-control" type="radio"></input>
              <label>Absurd</label>
            </form>
            <div class="btn-container">
              <button @click="startGame()" class="btn btn-pimary">Get Started</button>
            </div>
          </div>
        </div>
        <div class="container-fluid container-main">
            <div class="row">
                <div class="col-md-12">
                    <h1>The Current Question!</h1>
                    <div class="container">
                        <p>{{ state.singleQuestion }}</p>
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
            'state': {
                'showSplash': true,
                'questions': [],
                'singleQuestion': '',
                'chosenCategory': ''
            }
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
                this.$set('state.questions', questions)
            })
        }
    },
    'methods': {
        'startGame': function () {
            var chosenCategory = this.state.chosenCategory
            if (chosenCategory === 'silly') {
                console.log('silly')
            } else if (chosenCategory === 'deep') {
                console.log('deep')
            } else if (chosenCategory === 'hypothetical') {
                console.log('hypothetical')
            } else if (chosenCategory === 'absurd') {
                console.log('absurd')
            }
            this.state.showSplash = false
        },
        'nextQuestion': function () {
            this.state.singleQuestion = this.state.questions.shift().body
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

.splash-screen {
  position: absolute;
  display: flex;
  justify-content: center;
  width: 100%;
  height: 100vh;
  background-color: black;
  z-index: 1;
}

ol li {
  font-size: .8rem;
  line-height: 1.6rem;
}

.btn-container {
  display: flex;
  justify-content: center;
  button {
    margin-top: 1em;
  }
}

.instruction-dialog {
  padding: 2em;
  margin: auto;
  width: 60%;
  min-width: 300px;
  height: 50em;
  background-color: #eee;
  color: #444;

  h2 {
    text-align: center;
    font-size: 2rem;
  }

  p {
    color: #444;
  }
}

form.category {
  text-align: center;

  label {
    padding-left: .1em;
    padding-right: 1em;
  }
}

</style>
