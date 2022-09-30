<template>
  <div class="home text-center">
    <h1>stAcculynx</h1>
    <div v-if="isLoading">...LOADING...</div>
    <div v-else>
      <div
        class="row my-1"
        v-for="question in questions"
        v-bind:key="question.question_id"
      >
        <router-link
          class="btn btn-primary"
          :to="'/answers/' + question.question_id"
        >
          {{ question.title }}
        </router-link>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import Answer from "@/model/Answer";
import Question from "@/model/Question";
import { defineComponent } from "vue";

export default defineComponent({
  name: "Home",
  data: function () {
    var questions: Question[] = [];
    return {
      isLoading: true,
      questions: questions,
    };
  },
  async mounted() {
    var apiBase = "https://api.stackexchange.com/2.3/";

    var answerUrl =
      apiBase +
      "answers?pagesize=100&order=desc&sort=activity&site=stackoverflow";

    var answers = await fetch(answerUrl);
    var answerResults: { items: Answer[] } = await answers.json();

    var appropriateAnswers = answerResults.items
      .filter((x) => x.is_accepted)
      .slice(0, 7);

    appropriateAnswers.forEach(async (x) => {
      var questionUrl =
        apiBase +
        "questions/" +
        x.question_id +
        "?order=desc&sort=activity&site=stackoverflow";

      var response = await fetch(questionUrl);
      var questionResult: { items: Question[] } = await response.json();
      this.questions.push(questionResult.items[0]);
    });

    this.isLoading = false;
  },
});
</script>
