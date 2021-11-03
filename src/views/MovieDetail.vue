<template>
  <div class="movie-detail">
    <img :src="movie.Poster" :alt="'Affiche du film ' + movie.Title" />
    <div class="type">{{ movie.Type }}</div>
    <div class="detail">
      <p class="year">{{ movie.Year }}</p>
      <h3>{{ movie.Title }}</h3>
    </div>

    <p>{{ movie.Plot }}</p>
  </div>
</template>
<script>
import { ref, onBeforeMount } from "vue";
import { useRoute } from "vue-router";
import env from "@/env.js";
export default {
  setup() {
    const movie = ref({});
    const route = useRoute();

    onBeforeMount(() => {
      // appel api en fonction de l'id du film avec description longue
      fetch(
        `https://www.omdbapi.com/?apikey=${env.apikey}&i=${route.params.id}&plot=full`
      )
        .then((response) => response.json())
        .then((data) => {
          movie.value = data;
        });
    });

    return {
      movie,
    };
  },
};
</script>

<style lang="scss">
.movie-detail {
  padding: 16px;
  h2 {
    color: #fff;
    font-size: 28px;
    font-weight: 600;
    margin-bottom: 16px;
  }
  .featured-img {
    display: block;
    max-width: 300px;
    margin-bottom: 16px;
  }
  p {
    display: block;
    max-width: 700px;
    color: #fff;
    font-size: 18px;
    line-height: 1.4;
  }
}


@media screen and (max-width: 1280px) {
  .movie-detail {
    padding: 16px;
    h2 {
      color: #fff;
      font-size: 28px;
      font-weight: 600;
      margin-bottom: 16px;
    }
    .featured-img {
      display: block;
      max-width: 200px;
      margin-bottom: 16px;
    }
    p {
      color: #fff;
      font-size: 18px;
      line-height: 1.4;
    }
  }
}
</style>
