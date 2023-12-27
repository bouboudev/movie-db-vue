<template>
  <div class="home">
    <div class="feature-card">
    </div>
    <form @submit.prevent="SearchMovies()" class="search-box">
      <input
      type="search"
      placeholder="Quel film recherches-tu ?"
      v-model="search"
      />
    <input type="submit" value="Rechercher" />
  </form>
    
    <div class="movies-list">
      <div class="movie" v-for="movie in movies" :key="movie.id">
        <router-link v-bind:to="'/movie/' + movie.id" class="movie-link">
          <div class="product-image">
            <div class="vote-average">
              <CircularPercentage :voteAverage="movie.vote_average" :widthDynamic="widthDynamic"/>
            </div>
            <img :src="movie.poster_path ? getImageUrl(movie.poster_path) : require('@/assets/no_image.jpg')" :alt="'Affiche du film ' + movie.title" />
            
          </div>
          <div class="detail">
            <p class="genre" v-for="genre in movie.genre" :key="genre.id">
              {{ genre }}
            </p>
            <h3>{{ movie.title }}</h3>
            <p class="year">{{ typeof movie.release_date === 'string' ? movie.release_date.slice(0, 4) : '' }}</p>
          </div>
        </router-link>
      </div>
    </div>
  </div>
</template>



<script>
import { ref, onMounted, watch } from "vue";
import env from "@/env.js";
import { getImageUrl } from "../utils/image";
import CircularPercentage from "../components/CircularPercentage.vue";

export default {
  components: {
    CircularPercentage,
  },
  setup() {
    const search = ref(localStorage.getItem('search') || "");
    const movies = ref([]);
    const isMobile = window.matchMedia('(max-width: 768px)').matches;


    const clearSearch = () => {

      search.value = "";

      clearCache();

      SearchTopMovies();
    };

    const SearchMovies = async () => {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/search/movie?api_key=${env.apikey2}&query=${search.value}`);
        const data = await response.json();

        movies.value = data.results;

        const genrePromises = movies.value.map(movie => GetNameGenre(movie.genre_ids));
        const genreArrays = await Promise.all(genrePromises);

        movies.value.forEach((movie, index) => {
          movie.genre = genreArrays[index];
        });


        cacheResults(movies.value);

      } catch (error) {
        console.error('Erreur lors de la récupération des meilleurs films :', error);
      }
    };

    const SearchTopMovies = async () => {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/movie/top_rated?api_key=${env.apikey2}`);
        const data = await response.json();

        movies.value = data.results;

        const genrePromises = movies.value.map(movie => GetNameGenre(movie.genre_ids));
        const genreArrays = await Promise.all(genrePromises);

        movies.value.forEach((movie, index) => {
          movie.genre = genreArrays[index];
        });

      } catch (error) {
        console.error('Erreur lors de la récupération des meilleurs films :', error);
      }
    };

    const GetNameGenre = async (id) => {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/genre/movie/list?api_key=${env.apikey2}`);
        const data = await response.json();

        const genresMap = new Map();
        data.genres.forEach(genre => {
          genresMap.set(genre.id, genre.name);
        });

        const genreNames = id.map(genreId => genresMap.get(genreId));
        return genreNames;
      } catch (error) {
        console.error('Erreur lors de la récupération des genres :', error);
        return [];
      }
    };


    const cacheResults = (results) => {
      localStorage.setItem('searchResults', JSON.stringify(results));
    };


    const getCachedResults = () => {
      const cachedResults = localStorage.getItem('searchResults');

      if (cachedResults) {
        return JSON.parse(cachedResults);
      }

      return null;
    };


    const clearCache = () => {
      localStorage.removeItem('searchResults');
    };


    const saveSearchToLocalStorage = () => {
      localStorage.setItem('search', search.value);
    };

    onMounted(() => {

      const cachedResults = getCachedResults();

      if (cachedResults) {
        movies.value = cachedResults;
      } else {

        SearchTopMovies();
      }


      watch(search, () => {
        saveSearchToLocalStorage();
      });
      watch(search, () => {
        if (search.value.length === 0) {
          clearSearch();
        }
      });
    });

    return {
      search,
      movies,
      SearchMovies,
      SearchTopMovies,
      getImageUrl,
      GetNameGenre,
      CircularPercentage,
      isMobile,
      widthDynamic: isMobile ? '65%' : '55%',
      clearCache,
      clearSearch,
    };
  },
};
</script>





<style lang="scss">
.home {
  .feature-card {
    position: relative;
    .featured-img {
      display: block;
      width: 100%;
      height: 300px;
      object-fit: cover;
      position: relative;
      z-index: 0;
    }
    .detail {
      position: absolute;
      left: 0;
      right: 0;
      bottom: 0;
      background-color: rgba(0, 0, 0, 0.6);
      padding: 16px;
      z-index: 1;
      h3 {
        color: #fff;
        margin-bottom: 16px;
      }
      p {
        color: #fff;
      }
    }
  }
  .search-box {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 16px;
    input {
      display: block;
      appearance: none;
      border: none;
      outline: none;
      background: none;
      &[type="search"] {
        width: 50%;
        color: #fff;
        background-color: #496583;
        font-size: 20px;
        padding: 10px 16px;
        border-radius: 8px;
        margin-bottom: 15px;
        transition: 0.4s;
        &::placeholder {
          color: #f3f3f3;
        }
        &:focus {
          box-shadow: 0px 3px 6px rgba(0, 0, 0, 0.2);
        }
      }
      &[type="submit"] {
        width: 50%;
        max-width: 200px;
        background-color: #42b883;
        padding: 16px;
        border-radius: 8px;
        color: #fff;
        font-size: 20px;
        text-transform: uppercase;
        transition: 0.4s;
        &:active {
          background-color: #3b8070;
        }
      }
    }
  }
  .movies-list {
    display: flex;
    flex-wrap: wrap;
    margin: 0px 8px;
    .movie {
      max-width: 20%;
      flex: 1 1 20%;
      padding: 16px 8px;
      .movie-link {
        display: flex;
        flex-direction: column;
        height: 100%;
        .product-image {
          position: relative;
          display: block;
          img {
            display: block;
            width: 100%;
            height: 100%;
            object-fit: cover;
          }
          .vote-average {
              position: absolute;
              bottom: -12px;
              left: -40px;
              z-index: 1;
            }
        }
        .detail {
          background-color: #496583;
          padding: 16px 8px;
          flex: 1 1 100%;
          border-radius: 0px 0px 8px 8px;
          .year {
            color: #aaa;
            font-size: 14px;
          }
          h3 {
            color: #fff;
            font-weight: 600;
            font-size: 18px;
          }
          p {
            margin: 4px
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
      }
    }
  }
}

@media screen and (max-width: 1280px) {
  .home {
    .feature-card {
      position: relative;
      .featured-img {
        display: block;
        width: 100%;
        height: 300px;
        object-fit: cover;
        position: relative;
        z-index: 0;
      }
      .detail {
        position: absolute;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: rgba(0, 0, 0, 0.6);
        padding: 16px;
        z-index: 1;
        h3 {
          color: #fff;
          margin-bottom: 16px;
        }
        p {
          color: #fff;
        }
      }
    }
    .search-box {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      padding: 16px;
      input {
        display: block;
        appearance: none;
        border: none;
        outline: none;
        background: none;
        &[type="search"] {
          width: 100%;
          color: #fff;
          background-color: #496583;
          font-size: 20px;
          padding: 10px 16px;
          border-radius: 8px;
          margin-bottom: 15px;
          transition: 0.4s;
          &::placeholder {
            color: #f3f3f3;
          }
          &:focus {
            box-shadow: 0px 3px 6px rgba(0, 0, 0, 0.2);
          }
        }
        &[type="submit"] {
          width: 100%;
          max-width: 300px;
          background-color: #42b883;
          padding: 16px;
          border-radius: 8px;
          color: #fff;
          font-size: 20px;
          text-transform: uppercase;
          transition: 0.4s;
          &:active {
            background-color: #3b8070;
          }
        }
      }
    }
    .movies-list {
      display: flex;
      flex-wrap: wrap;
      margin: 0px 8px;
      .movie {
        max-width: 50%;
        flex: 1 1 50%;
        padding: 16px 8px;
        .movie-link {
          display: flex;
          flex-direction: column;
          height: 100%;
          .product-image {
            position: relative;
            display: block;
            img {
              display: block;
              width: 100%;
              height: 275px;
              object-fit: cover;
            }
            .vote-average {
              bottom: -7px;
              left: -29px; 
              z-index: 1; 
          }
          }
          .detail {
            background-color: #496583;
            padding: 16px 8px;
            flex: 1 1 100%;
            border-radius: 0px 0px 8px 8px;
            .year {
              color: #aaa;
              font-size: 14px;
            }
            h3 {
              color: #fff;
              font-weight: 600;
              font-size: 18px;
            }
          }
        }
      }
    }
  }
}

</style>
