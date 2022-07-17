<template>
    <div>
        <input v-model="userId">
        <button @click="getQiitaData()">取得開始</button>
        <div v-if="isClick">
            <table class="table table-striped">
                <tr>
                    <th>記事タイトル</th>
                    <th>URL</th>
                    <th>LGTM数</th>
                    <th>投稿日時</th>
                </tr>
                <tr v-for="(item, index) in displayQiitaDataList" :key="index">
                    <td class="text-left">{{ item.title }}</td>
                    <td ><a :href="item.url">{{ item.url }}</a></td>
                    <td>{{ item.likes_count }}</td>
                    <td>{{ item.created_at }}</td>
                </tr>
            </table>
            <div>
                <h3>記事数 {{ totalArticle }}コ</h3>// h3で文字サイズ調整すな←
                <h3>LGTM数 {{ totalLGTM }}コ</h3>
            </div>
        </div>
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
            axios.get(`https://qiita.com/api/v2/users/${this.userId}/items?page=1&per_page=100`, {})
            .then(res => {
                let allQiitaData = [];
                allQiitaData = res.data;

                let displayQiitaDataList = [];
                let totalLGTM = 0;
                allQiitaData.forEach(function (item) {
                    displayQiitaDataList.push(item);
                    totalLGTM += item.likes_count;
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
