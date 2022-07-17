<template>
    <div>
    <header className="QiitaApp-header">
        <button @click="getQiitaData()">Vue.js</button>
        <button @click="getQiitaDataReact()">React</button>
        <div v-if="isClick">
            <table class="table table-striped">
                <tr v-for="(item, index) in displayQiitaDataList" :key="index" align="left">
                    <td class="text-left"><img :src="item.user.profile_image_url" width="50" height="50" loading="lazy" alt="img" />
<a :href="item.url" target="_blank" rel="noreferrer" className="QiitaApp-link">{{ item.title }}</a> {{item.created_at}}</td>
                </tr>
            </table>
            <div>
                <h3>記事数 {{ totalArticle }}コ</h3>// h3で文字サイズ調整すな←
            </div>
        </div>
    </header>
    </div>
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
        }
    },
    methods: {
        getQiitaData: function() {
            axios.get(`https://qiita.com/api/v2/tags/Vue.js/items?page=1&per_page=20`, {})
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
    }
}

</script>
