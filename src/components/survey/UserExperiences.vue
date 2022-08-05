<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored experiences found. Star adding some survey results first.
      </p>
      <p v-else-if="!isLoading && errorMessage"></p>
      <ul v-else>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
const surveysUrl =
  "https://vue-axios-backend-default-rtdb.firebaseio.com/surveys.json";
import SurveyResult from "@/components/survey/SurveyResult.vue";

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      errorMessage: null,
    };
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      this.errorMessage = null;
      fetch(surveysUrl)
        .then((response) => {
          if (response.ok) {
            return response.json();
          }
        })
        .then((data) => {
          this.isLoading = false;
          const dataResults = [];
          for (const id in data) {
            dataResults.push({
              id: id,
              name: data[id].name,
              rating: data[id].rating,
            });
          }
          this.results = dataResults;
        })
        .catch(() => {
          this.isLoading = false;
          this.errorMessage = "Error";
        });
    },
  },
  mounted() {
    this.loadExperiences();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
