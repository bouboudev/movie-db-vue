<template>
  <div class="home">
    <div class="feature-card">
      <img src="../assets/cinema.jpg" alt="cinema" class="featured-img" />
      <div class="detail">
        <h3>Cinema</h3>
        <p>
          Le cinéma est un art du spectacle. En français, il est désigné comme
          le « septième art », d'après l'expression du critique Ricciotto Canudo
          dans les années 19201. L’art cinématographique se caractérise par le
          spectacle proposé au public sous la forme d’un film, c’est-à-dire d’un
          récit (fictionnel ou documentaire), véhiculé par un support (pellicule
          souple, bande magnétique, contenant numérique) qui est enregistré puis
          lu par un mécanisme continu ou intermittent qui crée l’illusion
          d’images en mouvement, ou par un enregistrement et une lecture
          continus de données informatiques.
        </p>
      </div>
    </div>
    <form @submit.prevent="SearchMovies()" class="search-box">
      <input
        type="text"
        placeholder="Quel film recherches-tu ?"
        v-model="search"
      />
      <input type="submit" value="Rechercher" />
    </form>

    <div class="movies-list">
      <div class="movie" v-for="movie in movies" :key="movie.imdbID">
        <router-link v-bind:to="'/movie/' + movie.imdbID" class="movie-link">
          <div class="product-image">
            <img :src="movie.Poster" :alt="'Affiche du film ' + movie.Title" />
            <div class="type">{{ movie.Type }}</div>
          </div>
          <div class="detail">
            <p class="year">{{ movie.Year }}</p>
            <h3>{{ movie.Title }}</h3>
          </div>
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import env from "@/env.js";

export default {
  setup() {
    const search = ref("");
    const movies = ref([]);
    const SearchMovies = () => {
      if (search.value != "") {
        fetch(`https://www.omdbapi.com/?apikey=${env.apikey}&s=${search.value}`)
          .then((response) => response.json())
          .then((data) => {
            movies.value = data.Search;
            // vider le input
            search.value = "";
          });
      }
    };
    return {
      search,
      movies,
      SearchMovies,
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
      &[type="text"] {
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
          .type {
            position: absolute;
            padding: 8px 16px;
            background-color: #42b883;
            color: #fff;
            bottom: 16px;
            left: 0px;
            text-transform: capitalize;
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
        &[type="text"] {
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
            .type {
              position: absolute;
              padding: 8px 16px;
              background-color: #42b883;
              color: #fff;
              bottom: 16px;
              left: 0px;
              text-transform: capitalize;
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
