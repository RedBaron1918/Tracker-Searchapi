<template>
  <main>
    <h1>AnimeTracker</h1>

    <form @submit.prevent="SearchAnime">
      <input type="text" v-model="query" />
      <button type="submit" class="button">Search</button>
    </form>
    <div class="results" v-if="searchResults.length > 0">
      <div class="result" v-for="anime in searchResults" :key="anime.id">
        <img :src="anime.images.jpg.image_url" alt="" />
        <div class="details">
          <h3>{{ anime.title }}</h3>
          <p :title="anime.synopsis" v-if="anime.synopsis">
            {{ anime.synopsis.slice(0, 100) }}
          </p>
          <span class="flex-1"></span>
          <button @click="addAnime(anime)">Add To Tracker</button>
        </div>
      </div>
    </div>

    <div class="myanime" v-if="animeList.length > 0">
      <h2>Anime List</h2>
      <div v-for="anime in AnimeListAC" :key="anime.id" class="anime">
        <img :src="anime.image" alt="" />
        <h3>{{ anime.title }}</h3>
        <div class="flex-1"></div>
        <span class="episodes">
          {{ anime.watchedEpisodes }} / {{ anime.totalEpisodes }}
        </span>
        <button
          v-if="anime.totalEpisodes !== anime.watchedEpisodes"
          @click="icreaseWatchedEpisodes(anime)"
          class="button"
        >
          +
        </button>
        <button
          v-if="anime.watchedEpisodes > 0"
          @click="decrease(anime)"
          class="button"
        >
          -
        </button>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      query: "naruto",
      animeList: [],
      searchResults: [],
    };
  },
  computed: {
    AnimeListAC() {
      return this.animeList.sort((a, b) => {
        return a.title.localeCompare(b.title);
      });
    },
  },

  methods: {
    async SearchAnime() {
      const url = `https://api.jikan.moe/v4/anime?q=${this.query}`;
      let res = await fetch(url);
      let data = await res.json();
      this.searchResults = data.data;
      console.log(this.searchResults);
    },
    addAnime(anime) {
      this.searchResults = [];
      this.query = "";
      this.animeList.push({
        id: anime.mal_id,
        title: anime.title,
        image: anime.images.jpg.image_url,
        totalEpisodes: anime.episodes,
        watchedEpisodes: 0,
      });
      localStorage.setItem("animeList", JSON.stringify(this.animeList));
    },
    icreaseWatchedEpisodes(anime) {
      anime.watchedEpisodes++;
      localStorage.setItem("animeList", JSON.stringify(this.animeList));
    },
    decrease(anime) {
      anime.watchedEpisodes--;
      localStorage.setItem("animeList", JSON.stringify(this.animeList));
    },
  },
  mounted() {
    this.animeList = JSON.parse(localStorage.getItem("animeList")) || [];
  },
  created() {
    this.SearchAnime();
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Montserrat", sans-serif;
}
body {
  background-color: #eee;
}
main {
  margin: 0 auto;
  max-width: 780px;
  padding: 20px;
}
h1 {
  text-align: center;
  margin-bottom: 20px;
  color: #888;
}
h1:hover {
  color: rgb(80, 80, 80);
}
form {
  display: flex;
  max-width: 480px;
  margin: 0 auto 1.5rem;
}

form input {
  appearance: none;
  outline: none;
  border: none;
  background-color: white;
  display: block;
  color: #888;
  font-size: 1.1rem;
  padding: 0.5rem 1rem;
  width: 100%;
}
.button {
  appearance: none;
  outline: none;
  border: none;
  background: none;
  cursor: pointer;
  display: block;
  padding: 0.5rem 1rem;
  background-image: linear-gradient(
    to right,
    rgb(105, 19, 131) 50%,
    darkviolet 50%
  );
  background-size: 200%;
  color: white;
  font-size: 1.125rem;
  font-weight: bold;
  text-transform: uppercase;
  transition: 0.4s;
}
.button:hover {
  background-position: right;
}
.results {
  background-color: #fff;
  border-radius: 0.5rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  max-height: 480px;
  overflow-y: scroll;
  margin-bottom: 1.5rem;
}
.result {
  display: flex;
  margin: 1rem;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  transition: 0.4s;
}
.result img {
  width: 100px;
  border-radius: 1rem;
  margin-right: 1rem;
}
.details {
  flex: 1 1 0%;
  display: flex;
  align-items: flex-start;
  flex-direction: column;
}
.details h3 {
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
}
.details p {
  font-size: 0.875rem;
  margin-bottom: 1rem;
}
.details .button {
  margin-left: auto;
}
.flex-1 {
  display: block;
  flex: 1 1 0%;
}
.myanime h2 {
  color: #888;
  font-weight: 400;
  margin-bottom: 1.5rem;
}
.myanime .anime {
  display: flex;
  align-items: center;
  margin-bottom: 1.5rem;
  background-color: #fff;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
}
.anime img {
  width: 72px;
  height: 72px;
  object-fit: cover;
  border-radius: 1rem;
  margin-right: 1rem;
}
.anime h3 {
  color: #888;
  font-size: 1.125rem;
}
.anime .episodes {
  margin-right: 1rem;
  color: #888;
}
.anime .button:first-of-type {
  margin-right: 0.5rem;
}
.anime .button:last-of-type {
  margin-right: 0;
}
</style>
