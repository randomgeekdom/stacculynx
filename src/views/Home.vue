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
    var questionUrl =
      apiBase +
      "questions?pagesize=100&order=desc&sort=activity&site=stackoverflow";

    var response = await fetch(questionUrl);
    var results: { items: Question[] } = await response.json();

    this.questions = results.items
      .filter((x) => x.is_answered && x.answer_count > 1)
      .slice(0, 5);

    this.isLoading = false;
  },
});
</script>
