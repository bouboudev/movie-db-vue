<template>
  <div class="movie-detail" v-if="movie">
    <div>
      <router-link to="/">
        <button class="btn-back">Revenir en arrière</button>
      </router-link>
    </div>
    <div class="image-container">
      <img :src="getImageUrl(movie.poster_path)" :alt="'Affiche du film ' + movie.title" />
      <div class="vote-average" v-if="isValidNumber(movie.vote_average)">
        <CircularPercentage :voteAverage="movie.vote_average" :widthDynamic="widthDynamic" />
      </div>
    </div>
    <div class="detail">
      <h3>{{ movie.title }}</h3>
      <p class="year">{{ getFormattedDate(movie.release_date) }}</p>
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
import { getFormattedDate } from "../utils/date.js";
import CircularPercentage from "../components/CircularPercentage.vue";

export default {
  components: {
    CircularPercentage,
  },
  setup() {
    const movie = ref({});
    const route = useRoute();
    const isMobile = window.matchMedia('(max-width: 768px)').matches;
    const isValidNumber = (value) => typeof value === 'number' && !isNaN(value);

    onBeforeMount(() => {
      getMovie();
    });

    const getMovie = async () => {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/movie/${route.params.id}?api_key=${env.apikey2}`);
        const data = await response.json();

        movie.value = data;
        console.log('resultat :',data);
      } catch (error) {
        console.error('Erreur lors de la récupération des meilleurs films :', error);
      }
    };

    return {
      movie,
      getImageUrl,
      getFormattedDate,
      getMovie,
      CircularPercentage,
      isMobile,
      widthDynamic: isMobile ? '65%' : '55%',
      isValidNumber,
    };
  },
};
</script>

<style lang="scss">


.movie-detail {
  padding: 24px;
  background-color: #34495e;
  border-radius: 12px; 
  overflow: hidden;
  
  .image-container {
    position: relative;
  }


  .vote-average {
    position: absolute;
    bottom: -10px;
    left: -40px;
  }
  h3 {
    font-size: 32px;
    font-weight: 700;
    margin-bottom: 12px;
    color: #ecf0f1; // Couleur du texte
  }

  .featured-img {
    display: block;
    max-width: 100%;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); // Ombre légère
    margin-bottom: 16px;
  }

  p {
    max-width: 100%;
    color: #ecf0f1;
    font-size: 18px;
    line-height: 1.6;
    margin-bottom: 16px;
  }
  .genre {
    display: inline-block;
    padding: 4px 8px;
    color: #fff;
    font-size: 12px;
    margin: 2px 4px;
    background-color: #42b883;
  }
}

.btn-back {
  background: #ecf0f1;
  border: none;
  border-radius: 8px;
  padding: 12px 24px;
  font-size: 18px;
  font-weight: 600;
  color: #34495e; 
  margin-bottom: 24px;
  cursor: pointer;
  transition: background-color 0.3s ease-in-out;

  &:hover {
    background-color: #bdc3c7; 
  }
}

@media screen and (max-width: 768px) {
  .movie-detail {
    padding: 16px;

    img {
  width: 100%; 
  height: auto;
}

    h3 {
      font-size: 28px;
    }

    .featured-img {
      margin-bottom: 12px;
    }

    p {
      font-size: 16px;
      margin-bottom: 12px;
    }
    
  }
}


</style>
