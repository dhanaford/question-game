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
              <select :value="state.chosenCategory" v-model="state.chosenCategory" class="form-control">
                <option v-for="category in content.categories">{{category}}</option>
              </select>
            </form>
            <div class="btn-container">
              <button @click="startGame()" class="btn btn-pimary">Get Started</button>
            </div>
          </div>
        </div>
        <div class="container-main">
            <div class="flex">
              <p>{{ state.singleQuestion }}</p>
              <button class="btn btn-primary" @click="nextQuestion()">Next Question</button>
              <label>Change Category</label>
              <select @change="changeCategory()" v-model="state.chosenCategory" class="form-control">
                <option v-for="category in content.categories">{{category}}</option>
              </select>
            </div>
        </div>
    </div>
</template>

<script>
import _ from 'lodash'

export default {
    'data' () {
        return {
            'state': {
                'showSplash': true,
                'questions': [],
                'categorized': [],
                'singleQuestion': '',
                'chosenCategory': 'Select a category',
                'changedCategory': 'Change Category'
            },
            'content': {
                'categories': ['icebreaker', 'deep', 'hypothetical', 'relationships']
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
                // randomize questions.json
                while (currentIndex !== 0) {
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
            this.state.categorized = _.filter(this.state.questions, { 'category': this.state.chosenCategory })
            this.$set('state.categorized', this.state.categorized)
            this.state.singleQuestion = this.state.categorized[0].body
            this.state.showSplash = false
        },
        'changeCategory': function () {
            this.state.chosenCategory = _.filter(this.state.questions, { 'category': this.state.chosenCategory })
            this.$set('state.categorized', this.state.chosenCategory)
            this.state.singleQuestion = this.state.categorized[0].body
        },
        'nextQuestion': function () {
            this.state.singleQuestion = this.state.categorized.shift().body
        }
    },
    'computed': {
        'findCategories': function () {
            var test = _.find(this.state.questions, { 'category': this.state.chosenCategory })
            return test
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
  display: flex;
  justify-content: center;
  width: 100%;
  height: 100vh;
}

.flex {
  text-align: center;
  padding: 2em;
  margin: auto;
  width: 60%;
  min-width: 300px;
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
