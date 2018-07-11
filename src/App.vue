<template>
  <div id="app">
    <p>
      Задайте вопрос, на который можно ответить "да" или "нет":
      <input type="text" v-model="question">
    </p>
    <p>{{ answer }}</p>
  </div>
</template>

<script>
import axios from "axios";
import _ from "lodash";

export default {
  name: "app",
  data() {
    return {
      question: "",
      answer: "Пока вы не зададите вопрос, я не могу ответить!"
    };
  },
  watch: {
    question: function(newQuestion, _oldQuestion) {
      this.answer = "Ожидаю, когда вы закончите печатать...";
      this.debounceAnswer();
    }
  },
  created() {
    this.debounceAnswer = _.debounce(this.getAnswer, 500);
  },
  methods: {
    getAnswer() {
      if (this.question.indexOf("?") === -1) {
        this.answer = "Вопросы обычно заканчиваются вопросительным знаком. ;-)";

        return;
      }
      this.answer = "Думаю...";
      const vm = this;
      axios
        .get("https://yesno.wtf/api")
        .then(function(response) {
          vm.answer = _.capitalize(response.data.answer);
        })
        .catch(function(error) {
          vm.answer = "Ошибка, Не могу связаться с API. " + error;
        });
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
