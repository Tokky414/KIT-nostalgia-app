<template>
  <div class="main">
    <!-- ーーーーーーーーーーーーーーーーーー -->
    <!-- 話題提示を行うカルーセル 　-->
    <!-- ーーーーーーーーーーーーーーーーーー　　-->
    <!-- v-modelでリアルタイム取得 -->
    <v-carousel
      :continuous="true"
      hide-delimiter-background
      height="150"
      v-model="selectedNum"
      style="position: fixed; top: 0; z-index: 999"
    >
      <v-carousel-item v-for="(topic, i) in topics" :key="i">
        <v-sheet :color="topic.color" height="100%">
          <div class="d-flex fill-height justify-center align-center mx-15">
            <div
              class="text-center text-h5 text-sm-h4 font-weight-bold white--text"
            >
              {{ topic.title }}
            </div>
          </div>
        </v-sheet>
      </v-carousel-item>
    </v-carousel>

    <!-- ーーーーーーーーーーーーーーーーーー -->
    <!-- ノスタルジア画像の表示 -->
    <!-- ーーーーーーーーーーーーーーーーーー　　-->
    <!-- フリックによる切り替え -->
    <v-container
      fluid
      style="max-width: 95%; padding-top: 150px"
    >
    <!-- menuボタン -->
    <v-speed-dial
      v-model="myfab"
      absolute
      bottom
      left
      class="mb-3"
      direction="right"
      transition="scale-transition"
      fixed
    >
      <template v-slot:activator>
        <v-btn
          v-model="myfab"
          color="blue-grey darken-3"
          style="opacity: 0.70;"
          elevation="10"
          large
          dark
          fab
        >
          <v-icon v-if="myfab">
            mdi-close
          </v-icon>
          <v-icon v-else>
            mdi-menu
          </v-icon>
        </v-btn>
      </template>
      <v-btn
        fab
        dark
        color="blue-grey darken-2"
        @click="$router.go(-1)"
      >
        <v-icon>mdi-arrow-u-left-top</v-icon>
      </v-btn>
      <v-btn
        fab
        dark
        color="blue-grey darken-1"
        to="/" nuxt
      >
        <v-icon>mdi-home</v-icon>
      </v-btn>
      <v-btn
        fab
        dark
        color="blue-grey darken"
        to="/settings" nuxt
      >
        <v-icon>mdi-cog</v-icon>
      </v-btn>
    </v-speed-dial>
    <!-- ソートボタン -->
    <v-btn
      @click="sortContents()"
      :color="selectedTopic.color"
      height="80px"
      width="80px"
      style="opacity: 0.80;"
      class="mb-15"
      elevation="10"
      fab
      dark
      absolute
      right
      bottom
      fixed
    >
      <v-icon x-large>{{ isAscendingOrder ? 'mdi-sort-descending' : 'mdi-sort-ascending' }}</v-icon>
    </v-btn>
    <!-- 縦の要素 -->
      <v-row
      v-touch="{
        left: () => swipe('Left'),
        right: () => swipe('Right'),
        up: () => swipe('Up'),
        down: () => swipe('Down'),
      }">
        <!-- 横の要素 -->
        <v-col
          v-for="(content, index) in selectedTopic.contents"
          :key="index"
          cols="12"
          sm="6"
          md="3"
          class="d-flex child-flex"
        >
          <v-card
            hover
            class="mx-auto mt-5 transition-swing"
            height="auto"
            width="auto"
          >
            <div class="d-flex justify-center align-center ma-5">
              <v-img :src="'..' + selectedTopic.image_path + content.img" contain height="200px" width="200px">
                <!-- 年代のチップを表示、オーバーレイ -->
                <v-sheet 
                style="position: absolute; top: 0; right: 0; font-weight: bold;"
                rounded
                elevation="3">
                  <v-chip
                    class="ma-0"
                    :color="selectedTopic.color"
                    outlined
                    label
                  >
                    <!-- アイコン -->
                    <v-icon left>
                    </v-icon>
                    {{ content.gen }}
                  </v-chip>
              </v-sheet>
              </v-img>
            </div>
            <v-divider></v-divider>
            <v-card-title class="font-weight-bold grey--text text--darken-2">
              {{ content.title }}
            </v-card-title>
          </v-card>
        </v-col>
        <!-- 最後の要素に、新規追加カード -->
        <v-col cols="12" sm="6" md="3" class="d-flex child-flex">
          <v-card
            hover
            class="mx-auto mt-5 transition-swing"
            height="auto"
            width="auto"
          >
            <div class="d-flex justify-center align-center ma-5">
              <v-btn
                color="primary darken-2"
                depressed
                fab
                height="100px"
                width="100px"
                class="ma-12"
              >
                <v-icon large color="white">mdi-plus</v-icon>
              </v-btn>
            </div>
            <v-divider></v-divider>
            <v-card-title class="font-weight-bold grey--text text--darken-2">
              新しいノスタルジアを追加
            </v-card-title>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>


<script>
// jsonをインポート
import topicsJson from "../static/json/temp.json";
// 変数
export default {
  data() {
    return {
      selectedNum: 0, // カルーセルのページ管理
      getTopics: topicsJson.topics, // jsonデータ(現在はここで表示しているが、年齢に応じて変更する場合は別途関数で処理)
      topics: [],
      profiles: null, // formから受け取り
      ages: [],
      isAscendingOrder: false,  // 昇順かどうかを示すフラグ
      myfab: false, // menu
    };
  },
  // 起動時に実行
  created() {
    // sessionStorageからデータを取得
    const profiles = sessionStorage.getItem("profilesJson");
    if (profiles) {
      // json parse
      this.profiles = JSON.parse(profiles);
      console.log(this.profiles);
    } else {
      this.profiles = null;
    }

    // ローカルストレージからデータを読み込む
    const storedData = localStorage.getItem('nostalgiaData');
    if (storedData) { // データがあればそれを表示
      this.topics = JSON.parse(storedData);
      //console.log(this.topics);
      this.topics = this.topics.filter(topic => topic.isVisible);
      // if(!this.topics){

      // }
    } else { // なければ元のjsonを表示
      this.topics = this.getTopics;
    }
  },
  // 変更時に実行
  computed: {
    // カルーセル変更に伴い、それに応じたトピックを算出
    selectedTopic() {
      return this.topics[this.selectedNum];
    },
  },
  // 通常の関数
  methods: {
    // スワイプで次の要素へ移動する機能
    swipe(direction) {
      switch (direction) {
        case "Right":
          if (this.selectedNum > 0) {
            this.selectedNum--;
          } else {
            // -方向に行った場合、最初の要素まで戻る
            this.selectedNum = this.topics.length - 1;
            this.currentItem = 0;
          }
          break;
        case "Left":
          if (this.selectedNum < this.topics.length - 1) {
            this.selectedNum++;
          } else {
            // 最後の要素まで行った場合、最初の要素に戻る
            this.selectedNum = 0;
          }
          break;
        default:
          console.log(direction);
      }
    },
    // ソートボタン選択時に実行
    sortContents() {
      // 昇順・降順の切り替え
      const orderMultiplier = this.isAscendingOrder ? 1 : -1;

      // Genでソートするロジック
      this.selectedTopic.contents.sort((a, b) => orderMultiplier * (a.gen - b.gen));

      // 切り替え
      this.isAscendingOrder = !this.isAscendingOrder;
    }
  },
};
</script>


<style>
.main {
  background-color: #f5f5f5;
}

/* Vuetifyにおける上書きスタイル 
　　　　　　　・Chromeの詳細でタグを確認
　　　　　　　・同名で上書きする
*/
/* v-cardの通常の影のスタイル */
.v-sheet.v-card:not(.v-sheet--outlined) {
  box-shadow: 0px 2px 4px -1px rgba(0, 0, 0, 0.2),
    0px 4px 5px 0px rgba(0, 0, 0, 0.14), 0px 1px 10px 0px rgba(0, 0, 0, 0.12) !important;
}
/* v-cardにホバーした際の影のスタイル */
.v-sheet.v-card--hover:hover {
  box-shadow: 0px 8px 10px -5px rgba(0, 0, 0, 0.2),
    0px 16px 24px 2px rgba(0, 0, 0, 0.14), 0px 6px 30px 5px rgba(0, 0, 0, 0.12) !important;
}
/* .v-application .elevation-16 {
    box-shadow: 0px 8px 10px -5px rgba(0, 0, 0, 0.2), 0px 16px 24px 2px rgba(0, 0, 0, 0.14), 0px 6px 30px 5px rgba(0, 0, 0, 0.12) !important;
  }
  .v-application .elevation-4 {
    box-shadow: 0px 2px 4px -1px rgba(0, 0, 0, 0.2), 0px 4px 5px 0px rgba(0, 0, 0, 0.14), 0px 1px 10px 0px rgba(0, 0, 0, 0.12) !important;
  } 
  */
</style>
