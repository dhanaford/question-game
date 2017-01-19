<template>
    <div id="app">
      <div class="container-main">
        <div v-if="state.showSplash" class="flex-main">
          <div class="content-wrapper instruction-dialog">
              <h4>In need of some good ol’ fashion conversation? <br /><b>Well look no further!</b></h4>
                <p>Generate questions at random to spark interesting conversation.</p>
                  <ul>
                    <li>Take turns answering a randomly generated question</li>
                    <li>Actually listen to each other before the next person answers</li>
                    <li>Build relationships</li>
                    <li>Break the Ice</li>
                    <li>Practice the art of listening as well as speaking</li>
                  </ul>
                <p>Take a quick glance at the app during awkward social situations, first dates, or if you just plain lack the wit to think of anything interesting on your own :)</p>
                <div class="category-chooser category-chooser__main">
                  <p>Current category:
                      <select @change="changeCategory()" v-model="state.chosenCategory">
                          {{state.currentCategory}}
                          <option v-for="category in content.categories">
                              {{category}}
                          </option>
                      </select>
                  </p>
                </div>
                <div class="btn-container">

              <button @click="startGame()" class="btn btn-primary btn-primary__main">Begin!</button>
            </div>
          </div>
        </div>
        <div v-if="state.showSplash === false" class="flex-main">
            <div class="content-wrapper content-wrapper__second">
              <div class="question">
                <p>{{ state.singleQuestion }}</p>
              </div>
              <button class="btn btn-primary" @click="nextQuestion()">Generate Question</button>
              <div class="category-chooser">
                <p>Current category:
                    <select @change="changeCategory()" v-model="state.chosenCategory">
                        {{state.currentCategory}}
                        <option v-for="category in content.categories">
                            {{category}}
                        </option>
                    </select>
                </p>
              </div>
              <div class="back-to-intro"><a href="" @click.prevent="backToIntro()">Back to intro</a></div>
            </div>
        </div>
    </div>
    <footer>
      <div class="row">
        <div class="col-xs-6 pull-left">
          <p>The Conversation Generator V 1.0.0-alpha</p>
        </div>
        <div class="col-xs-6 pull-right">
          <p class="copyright"><span>©</span> David Hanaford 2017</p>
      </div>
    </footer>
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
                'chosenCategory': 'all',
                'changedCategory': 'Change Category',
                'currentCategory': ''
            },
            'content': {
                'categories': ['all', 'icebreaker', 'deep', 'hypothetical', 'relationships']
            }
        }
    },
    'events': {
        'hook:ready': function () {
            // this.$http.get('//converstion-generator-questions.s3-website-us-east-1.amazonaws.com/questions.json').then((response) => {
            this.$http.get('../static/questions.json').then((response) => {
                // var questions = JSON.parse(response.data)
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
                this.state.questions = questions
            })
        }
    },
    'methods': {
        'startGame': function () {
            if (this.state.chosenCategory === 'all') {
                this.state.categorized = this.state.questions
            } else {
                this.state.categorized = _.filter(this.state.questions, { 'category': this.state.chosenCategory })
                this.state.currentCategory = this.state.categorized[0].category
                this.state.singleQuestion = this.state.categorized.body
            }
            this.state.showSplash = false
        },
        'changeCategory': function () {
            if (this.state.chosenCategory === 'all') {
                this.state.categorized = this.state.questions
            } else {
                this.state.categorized = _.filter(this.state.questions, { 'category': this.state.chosenCategory })
                this.state.currentCategory = this.state.categorized[0].category
                this.state.singleQuestion = this.state.categorized.body
            }
        },
        'nextQuestion': function () {
            this.state.singleQuestion = this.state.categorized.shift().body
        },
        'backToIntro': function () {
            this.state.showSplash = true
        }
    }
}
</script>

<style lang="scss">

input:focus,
select:focus,
textarea:focus,
button:focus {
    outline: none;
}

html {
    height: 100%;
    font-size: 16px;
}

body {
    background-color: #F9CA00;
    color: #b2afac;
    font-size: 62.5%;
}

a {
  color: #6BAB03;
}

ul {
  text-align: left;
  color: #444;
  font-size: 1.3em;
}

a:hover {
  color: #81C612;
}

button {
}

button:focus {
  outline: 2px solid #6BAB03 !important;
  background-color: transparent !important;
  color: #444 !important;
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

select  {
  font-weight: bold;
  color: #6BAB03;
  background-color: transparent !important;
  &:hover {
    border: 1px solid #81C612;
    cursor: pointer;
  }
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
  position: absolute;
  width: 100%;
  margin-left: 8px;
  margin-top: 35px;
  p {
    color: #444;
    text-transform: uppercase;
    font-size: .88rem;
    position: absolute;
    top: 87px;
    left: 12px;
  }
}

.container-main {
  display: flex;
  justify-content: center;
  width: 100%;
  height: 100vh;
}

.flex-main {
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
  padding-top: 10rem;
  padding-right: 5rem;
  max-height: 328px;
}

.content-wrapper__second {
  text-align: center;
}

.category-chooser__main {
  margin-top: -61px;
  margin-left: 30px;
}


.btn-primary {
  background-color: transparent;
  color: #444;
  border: 2px solid #6BAB03;
  border-radius: 0px;
  padding: .75em 1.5em;
  font-size: 2em;
  letter-spacing: 1.5px;
}

.btn-primary__main {
  border: 2px solid #0BBCDF;
}

.btn-primary:hover,
.btn-pimary:active,
.btn-primary:focus,
.btn-primary:visited,
.btn-primary:link {
    background-color: transparent;
    color: black;
    border: 2px solid #81C612;
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
  justify-content: flex-end;
  button {
    margin-top: 1.3em;
    padding: .5em 2em;
    font-size: 1.5em;
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

.back-to-intro {
  position: absolute;
  margin-left: 730px;
  padding-top: 2px;
  a {
    font-weight: bold;
    font-size: 1.3em;
  }
}

footer {
  background-color: #444;
  padding: 1em;
  p {
    color: #6BAB03;
    font-size: 1.3em;
    margin-bottom: 0;
  }
  p.copyright {
    text-align: right;
  }
}

</style>
