<template>
  <div>
    <header className="QiitaApp-header">
      <div id="app">
        <a href="https://mbp.hatenablog.com/entry/2021/08/12/082102" target="_blank" rel="noreferrer">Youtube APIを使って検索＋再生</a><br />
        <p>Youtube APIのテスト</p>
        <input v-model="keyword" placeholder="検索キーワードを入力" />
        <button @click="searchMovies">検索</button>
        <table class="table" v-show="results">
          <tr v-for="(movie, index) in results" v-bind:key="movie.id.videoId">
            <td>
              <a
                  v-bind:href="'https://www.youtube.com/watch?v=' + movie.id.videoId"
                  ><img width=640 height=360
                      v-bind:src="movie.snippet.thumbnails.medium.url"
              /></a>
            </td>
            <td>
              <b>{{ movie.snippet.title }}</b> <br />
              <span class="desc">{{
                movie.snippet.description
              }}</span>
            </td>
          </tr>
        </table>
      </div>
    </header>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "SearchYoutube",
  data: function() {
    return {
      results: null,
      keyword: "ヒカキン",
      params: {
        q: "",
        part: "snippet",
        type: "video",
        maxResults: "1", // 最大検索数
        order: "viewCount",
        key: import.meta.env.VITE_YOUTUBE_API_KEY 
      }
    };
  },
  props: {
    msg: String
  },
  methods: {
    searchMovies: function() {
      this.params.q = this.keyword;
      var self = this;
      axios
        .get("https://www.googleapis.com/youtube/v3/search", {
          params: this.params
        })
        .then(function(res) {
          self.results = res.data.items;
        })
    }
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
a {
  text-decoration: none;
  color: white;
}
</style>
