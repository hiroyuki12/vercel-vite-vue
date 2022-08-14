<template>
  <div>
    <header className="QiitaApp-header">
      <a href="https://mbp.hatenablog.com/entry/2021/08/12/082102" target="_blank" rel="noreferrer">Youtube APIを使って検索＋再生</a><br />
      <p>Youtube APIのテスト</p>
      <YouTube 
        src="https://www.youtube.com/watch?v=WJzSBLCaKc8" 
        @ready="onReady"
        ref="youtube" />
    </header>
    <div className="QiitaApp-footer">YouTube</div>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
import YouTube from 'vue3-youtube'

export default defineComponent({
  components: { YouTube },
  methods: {
    onReady() {
      this.$refs.youtube.playVideo()
    },
  },
})
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.QiitaApp-footer {
  position: fixed;
  bottom: 0px;
  width: 100%;
  height: 60px;
  background-color: #282c44;
  text-align: center;
  color: white;
}
.QiitaApp-header {
  background-color: #282c34;
  color: orange;
}
a {
  text-decoration: none;
  color: white;
}
</style>
