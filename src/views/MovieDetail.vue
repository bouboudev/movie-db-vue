<template>
  <div class="movie-detail">
    <div>
      <router-link to="/">
        <button class="btn-back">Revenir en arrière</button>
      </router-link>
    </div>
    <img :src="getImageUrl(movie.poster_path)" :alt="'Affiche du film ' + movie.title" />
    <div class="detail">
      <h3>{{ movie.title }}</h3>
      <p class="year">{{ movie.release_date }}</p>
      <p class="genre" v-for="genre in movie.genres" :key="genre.id">
              {{ genre.name }}
            </p>
    </div>

    <p>{{ movie.overview }}</p>
  </div>
</template>
<script>
import { ref, onBeforeMount } from "vue";
import { useRoute } from "vue-router";
import env from "@/env.js";
import { getImageUrl } from "../utils/image";

export default {
  setup() {
    const movie = ref({});
    const route = useRoute();

    onBeforeMount(() => {
      getMovie();
    });

    const getMovie = async () => {
      fetch(`https://api.themoviedb.org/3/movie/${route.params.id}?api_key=${env.apikey2}`)
          .then((response) => response.json())
          .then((data) => {
            movie.value = data;
            console.log('resultat :',data.genres);
          });

    };

    return {
      movie,
      getImageUrl,
      getMovie,
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
.btn-back {
  // style du bouton de retour
  background: #fff;
  border: none;
  border-radius: 4px;
  padding: 8px 16px;
  font-size: 16px;
  font-weight: 600;
  color: #35495e;
  margin-bottom: 16px;
  cursor: pointer;
  

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
