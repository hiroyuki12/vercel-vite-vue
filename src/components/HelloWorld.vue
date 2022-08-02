<template>
    <div>
    <header className="QiitaApp-header">
        <font color="red"><b>{{error}}</b></font><br />
        <a href="https://mbp.hatenablog.com/entry/2022/07/16/213404" target="_blank" rel="noreferrer" className="QiitaApp-link">VercelでVue 3 + Viteのアプリを作成(vercel-vite-vue)</a><br />
        <a href="https://mbp.hatenablog.com/entry/2022/07/16/222139" target="_blank" rel="noreferrer" className="QiitaApp-link">Vite + VueでQiitaAPIを使って記事情報を取得して表示、無限スクロール Vercel</a><br />
        <button @click="getQiitaData('react')">React</button>
        <button @click="getQiitaData('vue.js')">Vue.js</button>
        <button @click="getQiitaDataReact()">React</button>
        {{tag}}<br />
        <button @click="pageButtonClick()">__10__</button>{{page}}
        <div v-if="isClick">
          <table class="table table-striped">
            <tr v-for="(item, index) in displayQiitaDataList" :key="index" align="left">
              <td class="card-container">
                <img :src="item.user.profile_image_url" width="50" height="50" loading="lazy" alt="img" />
                <div class="card-text">
                  <a :href="item.url" target="_blank" rel="noreferrer" className="QiitaApp-link">{{ item.title }}</a> 
                  <div class="card-text2">
                    <p>{{item.created_at}} / {{item.tags[0].name}} / {{item.likes_count}}likes / {{item.user.items_count}}posts </p>
                  </div>
                </div>
              </td>
            </tr>
          </table>
        </div>
        <p v-if="isLoading">Loading .... page: {{page}}/20posts/{{20*(page-1)+1}}-</p>
        <p v-else>Not Loading. page: {{page}}/20posts/{{20*(page-1)+1}}-</p>
            <div>
                <h3>記事数 {{ totalArticle }}コ</h3>// h3で文字サイズ調整すな←
            </div>
    </header>
    </div>
<infinite-loading @infinite="getQiitaData"></infinite-loading>
</template>

<script>
import axios from "axios";

export default {
    data() {
        return {
            userId: "",
            displayQiitaDataList: "",
            totalArticle: 0,
            totalLGTM: 0,
            isClick: false,
            page: 0,
            tag: "Vue.js",
            //tag: "React",
            allQiitaData: [],
            top: 0,
            inner: 0,
            offset: 0,
            error: "",
            isLoading: false,
        }
    },
    methods: {
        getNextPage: function() {
          window.onscroll = () => {
            this.top = document.documentElement.scrollTop;
            this.inner = window.innerHeight;
            this.offset = document.documentElement.offsetHeight;

            if (
              window.innerHeight + document.documentElement.scrollTop !==
              document.documentElement.offsetHeight
            ) {
              return;
            }

            this.getQiitaData(this.tag);
          }
        },
        getQiitaData: function(tag) {
            this.tag = tag;
            this.isLoading = true;
            this.page = this.page + 1;
            axios.get(`https://qiita.com/api/v2/tags/${tag}/items?page=${this.page}&per_page=20`, {})
            .then(res => {
                let allQiitaData = [];
                allQiitaData = this.allQiitaData.concat(res.data);

                let displayQiitaDataList = [];
                let totalLGTM = 0;
                allQiitaData.forEach(function (item) {
                    displayQiitaDataList.push(item);
                    //totalLGTM += item.likes_count;
                })
                // forEach内でthis.displayQiitaDataListへ格納できないので外でやる
                this.displayQiitaDataList = displayQiitaDataList.sort();
                this.totalLGTM = totalLGTM;
                // total記事数を取得
                this.totalArticle = displayQiitaDataList.length;
                // clickによる表示の制御
                this.isClick = true;
                this.allQiitaData = allQiitaData;
            }).catch(err => {
                this.error = err.message;  // Request failed with status code 403
                this.error = "Rate limit exceeded";
            })
            //this.isLoading = false;
        },
        getQiitaDataReact: function() {
            this.tag = "React";
            axios.get(`https://qiita.com/api/v2/tags/React/items?page=1&per_page=20`, {})
            .then(res => {
                let allQiitaData = [];
                allQiitaData = res.data;

                let displayQiitaDataList = [];
                let totalLGTM = 0;
                allQiitaData.forEach(function (item) {
                    displayQiitaDataList.push(item);
                    //totalLGTM += item.likes_count;
                })
                // forEach内でthis.displayQiitaDataListへ格納できないので外でやる
                this.displayQiitaDataList = displayQiitaDataList.sort();
                this.totalLGTM = totalLGTM;
                // total記事数を取得
                this.totalArticle = displayQiitaDataList.length;
                // clickによる表示の制御
                this.isClick = true;
            })
        },
        pageButtonClick: function() {
            this.page = 10;
        },
        outputTest: function() {
            console.log(page);
        },
    },
    mounted() {
      this.getNextPage();
    },
    //watch: {
    //  tag: 'outputTest'
    //}
}

</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.QiitaApp-header {
  background-color: #282c34;
  color: orange;
}
.QiitaApp-link {
  color: white;
}
a {
  text-decoration: none;
}
.card-container{
  display: flex;
  height: 34px;
}

.card-text a{
  color: white;
  font-size: 14px;
  line-height: 1.2em;

  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
}

.card-text2{
  font-size: 11px;

  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 1;
}
</style>
