<template>
    <div>
    <header className="QiitaApp-header">
        <font color="red"><b>{{error}}</b></font><br />
        <a href="https://mbp.hatenablog.com/entry/2022/07/16/213404" target="_blank" rel="noreferrer" className="QiitaApp-link">VercelでVue 3 + Viteのアプリを作成(vercel-vite-vue)</a><br />
        <a href="https://mbp.hatenablog.com/entry/2022/07/16/222139" target="_blank" rel="noreferrer" className="QiitaApp-link">Vite + VueでQiitaAPIを使って記事情報を取得して表示、無限スクロール Vercel</a><br />
        <button @click="getQiitaData()">Vue.js</button>
        <button @click="getQiitaDataReact()">React</button>
        page: {{page}}, tag:Vue.js
        <div v-if="isClick">
            <table class="table table-striped">
                <tr v-for="(item, index) in displayQiitaDataList" :key="index" align="left">
                    <td class="text-left"><img :src="item.user.profile_image_url" width="50" height="50" loading="lazy" alt="img" />
<a :href="item.url" target="_blank" rel="noreferrer" className="QiitaApp-link">{{ item.title }}</a> {{item.created_at}}</td>
                </tr>
            </table>
        Page: {{page}}, tag: Vue.js, Loading: {{isLoading}}
        <br />
        <p v-if="isLoading">
          Loading .... page: {{page}}/20posts/{{20*(page-1)+1}}</p>
        <p v-else>Not Loading. page: {{page}}/20posts/{{20*(page-1)+1}}</p>
            <div>
                <h3>記事数 {{ totalArticle }}コ</h3>// h3で文字サイズ調整すな←
            </div>
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

            this.isLoading = true;
            this.getQiitaData();
          }
        },
        getQiitaData: function() {
            this.page = this.page + 1;
            axios.get(`https://qiita.com/api/v2/tags/Vue.js/items?page=${this.page}&per_page=20`, {})
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
            this.isLoading = false;
        },
        getQiitaDataReact: function() {
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
    },
    mounted() {
      this.getNextPage();
    }
}

</script>
