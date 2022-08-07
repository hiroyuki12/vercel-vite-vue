<template>
    <div>
    <header className="QiitaApp-header">
        <font color="red"><b>{{error}}</b></font>
        <font color="red"><b>{{errorMessage}}</b></font><br />
        <a href="https://mbp.hatenablog.com/entry/2022/07/16/213404" target="_blank" rel="noreferrer" className="QiitaApp-link">VercelでVue 3 + Viteのアプリを作成(vercel-vite-vue)</a><br />
        <a href="https://mbp.hatenablog.com/entry/2022/07/16/222139" target="_blank" rel="noreferrer" className="QiitaApp-link">Vite + VueでQiitaAPIを使って記事情報を取得して表示、無限スクロール Vercel</a><br />
        <button @click="tagButtonClick('React')">React</button>
        <button @click="tagButtonClick('Next.js')">Next.js</button>
        <button @click="tagButtonClick('Vue.js')">Vue.js</button>
        <button @click="tagButtonClick('Nuxt')">Nuxt.js</button>
        <button @click="tagButtonClick('Swift')">Swift</button>
        <button @click="tagButtonClick('Vim')">Vim</button>
        <button @click="tagButtonClick('Azure')">Azure</button>
        <button @click="tagButtonClick('AWS')">AWS</button>
        <button @click="tagButtonClick('.NET')">.NET</button>
        <button @click="tagButtonClick('Flutter')">Flutter</button>
        {{tag}}<br />
        page:<button @click="pageButtonClick('1')">__1__</button>
        __:<button @click="pageButtonClick('10')">__10__</button>
        __:<button @click="pageButtonClick('50')">__50__</button>{{page}}<br /><br />
        <div v-if="isClick">
          <table class="table table-striped">
            <tr v-for="(item, index) in displayQiitaDataList" :key="index" align="left">
              <td class="card-container">
                <img :src="item.user.profile_image_url" width="50" height="50" loading="lazy" alt="img" />
                <div class="card-text">
                  <a :href="item.url" target="_blank" rel="noreferrer" className="QiitaApp-link">{{ item.title }}</a> 
                  <div class="card-text2">
                    <p>{{item.updated_at}} / {{item.tags[0].name}} / {{item.likes_count}}likes / {{item.user.items_count}}posts </p>
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
    <div className="QiitaApp-footer">{{tag}} Page {{page}}/20posts</div>
    </div>
<infinite-loading @infinite="getQiitaData"></infinite-loading>
</template>

<script>
import axios from "axios";
import dayjs, { extend } from "dayjs";
// 相対日時のプラグインを有効化
import relativeTime from "dayjs/plugin/relativeTime";
extend(relativeTime);

export default {
    data() {
        return {
            displayQiitaDataList: "",
            totalArticle: 0,
            isClick: false,
            page: 1,
            tag: "Vue.js",
            allQiitaData: [],
            error: "",
            errorMessage: "",
            isLoading: false,
        }
    },
    methods: {
        clearButtonClick: function() {
          this.tag = "";
          this.allQiitaData.splice(0);
          this.displayQiitaDataList.splice(0);
        },
        tagButtonClick: function(tag) {
          this.tag = tag;
          //this.page = 0;
          this.allQiitaData = [];
          this.displayQiitaDataList = [];

          this.getQiitaData();
        },
        getNextPage: function() {
          window.onscroll = () => {
            if (
              window.innerHeight + document.documentElement.scrollTop !==
              document.documentElement.offsetHeight
            ) {
              return;
            }

            this.page = this.page + 1;
            this.getQiitaData();
          }
        },
        getQiitaData: function() {
            this.isLoading = true;
            axios.get(`https://qiita.com/api/v2/tags/${this.tag}/items?page=${this.page}&per_page=20`, {})
            .then(res => {
                let allQiitaData = [];
                allQiitaData = this.allQiitaData.concat(res.data);

                let displayQiitaDataList = [];
                allQiitaData.forEach(function (item) {
                    item.updated_at= dayjs(item.created_at).fromNow(true) // => days
                    displayQiitaDataList.push(item);
                })
                // forEach内でthis.displayQiitaDataListへ格納できないので外でやる
                this.displayQiitaDataList = displayQiitaDataList.sort();
                // total記事数を取得
                this.totalArticle = displayQiitaDataList.length;
                // clickによる表示の制御
                this.isClick = true;
                this.allQiitaData = allQiitaData;
            }).catch(err => {
                //this.error = err.message;  // Request failed with status code 403
                this.error = "Rate limit exceeded";
                this.errorMessage = err.message;  // Request failed with status code 403
            })
            //this.isLoading = false;
        },
        pageButtonClick: function(target) {
            const tmp = parseInt(target,10);
            this.page = tmp;
        },
        outputTest: function() {
            console.log(page);
        },
    },
    mounted() {
      this.getNextPage();
    },
    created() {
      this.getQiitaData();
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
