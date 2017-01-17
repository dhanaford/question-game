<template>
    <div id="app">
      <div class="container-main">
        <div v-if="state.showSplash" class="flex">
          <div class="content-wrapper instruction-dialog">
              <p><b>Generate some randomized question based on a category of your choosing or just generate a completely random question.</b> People love having good quality conversation and laughter. People also like staring at the computer screens and smart phones all day long. Why not combine both!</p>
              <p>There are no rules. As long as everyone gets an uninterrupted chance to answer the question, then this game can be rewarding and fun way to open up and learn about one and ither.</p>
            <form class="form-inline category">
              <p><b>Pick a category below to get started!</b></p>
              <select @change="startGame()" :value="state.chosenCategory" v-model="state.chosenCategory" class="form-control">
                <option v-for="category in content.categories">{{category}}</option>
              </select>
            </form>
          </div>
        </div>
        <div v-if="state.showSplash === false" class="flex">
              <div class="content-wrapper">
                <div class="question">
                  <p>{{ state.singleQuestion }}</p>
                </div>
                <button class="btn btn-primary" @click="nextQuestion()">Generate Question</button>
                <br />
                <br />
                <label>Change Category</label>
                <select @change="changeCategory()" v-model="state.chosenCategory" class="form-control form-control-sm category">
                  <option v-for="category in content.categories">{{category}}</option>
                </select>
                <div class="category-chooser">
                  <p>Current category: <span>{{state.currentCategory}}</span></p>
                </div>
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
                'changedCategory': 'Change Category',
                'currentCategory': ''
            },
            'content': {
                'categories': ['all', 'icebreaker', 'thought provoking', 'hypothetical', 'relationships']
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
            if (this.state.chosenCategory === 'all') {
                console.log('test')
            }
            this.state.categorized = _.filter(this.state.questions, { 'category': this.state.chosenCategory })
            this.$set('state.categorized', this.state.categorized)
            this.state.singleQuestion = this.state.categorized.body
            this.state.currentCategory = this.state.categorized[0].category
            this.state.showSplash = false
        },
        'changeCategory': function () {
            this.state.chosenCategory = _.filter(this.state.questions, { 'category': this.state.chosenCategory })
            this.$set('state.categorized', this.state.chosenCategory)
            this.state.singleQuestion = this.state.categorized.body
            this.state.currentCategory = this.state.categorized[0].category
        },
        'nextQuestion': function () {
            this.state.singleQuestion = this.state.categorized.shift().body
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
    background-color: #F9CA00;
    color: #b2afac;
    font-size: 62.5%;
}

h1 {
    font-size: 2.5rem;
}

h2 {
  text-align: center;
  font-size: 2rem;
  color: black;
  font-weight: bold;
}

h3, h4 {
  color: black;
}

p {
    font-size: 1rem;
    color: black;
}

.question {
  min-height: 90px;
  p {
    color: black;
    font-size: 1.9rem;
    font-weight: bold;
  }
}
.category-chooser {
  text-transform: capitalize;
  position: relative;
  p {
    color: #444;
    text-transform: uppercase;
    font-size: .88rem;
    position: absolute;
    top: 87px;
    left: 12px;
  }
  span {
    font-weight: bold;
    color: #6BAB03;
  }
}

.container-main {
  display: flex;
  justify-content: center;
  width: 100%;
  height: 100vh;
  padding-top: 5%;
}

.flex {
  text-align: center;
  margin: auto;
  width: 100%;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
  background: transparent url(/static/hero-bg.svg) no-repeat;
  background-size: contain;
  height: 700px;
  background-position-x: center;
}

.content-wrapper {
  margin-top: 159px;
  width: 41rem;
  margin: auto;
  padding-top: 9rem;
  padding-right: 5rem;
}

.btn-primary {
    background-color: #24aadd;
    color: white;
    border: 1px solid #0D8BBB;
    border-radius: 0px;
}

.btn-primary:hover,
.btn-pimary:active,
.btn-primary:focus,
.btn-primary:visited,
.btn-primary:link {
    background-color: #56D1FF;
    color: white;
    border: 1px solid #0D8BBB;
}

.splash-screen {
  position: absolute;
  display: flex;
  justify-content: center;
  width: 100%;
  height: 100vh;
  background-color: #F9CA00;
  z-index: 1;
}

ol li {
  font-size: .8rem;
  line-height: 1.6rem;
  color: black;
  text-align: left;
}

select.category {
  width: 295px;
  margin: auto;
}

.btn-container {
  display: flex;
  justify-content: center;
  button {
    margin-top: 1em;
    background-color: #24aadd;
    color: white;
    border: 1px solid #0D8BBB;
  }
}

.instruction-dialog {
  width: 44rem;
  padding: 9rem 7rem 7rem 0;
  p {
    color: black;
  }
}

label {
  color: black;
}

form.category {
  text-align: center;

  label {
    padding-left: .1em;
    padding-right: 1em;
  }
}

</style>
