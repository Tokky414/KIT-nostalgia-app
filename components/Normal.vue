<template>
    <div class="main"
    style="height: 100%;"
    v-touch="{
        left: () => swipe('Left'),
        right: () => swipe('Right')
      }">
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
        <v-btn
        fab
        dark
        color="blue-grey darken"
        @click="openDialog()"
        >
          <v-icon>mdi-format-quote-close</v-icon>
        </v-btn>
      </v-speed-dial>
      <!-- 縦の要素 -->
      <v-row class="mb-5">
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
              <v-img :src="selectedTopic.image_path + content.img" contain height="200px" width="200px">
              </v-img>
            </div>
            <v-divider></v-divider>
            <v-card-title class="font-weight-bold grey--text text--darken-2">
              {{ content.title }}
            </v-card-title>
          </v-card>
        </v-col>
      </v-row>
      <v-dialog
        v-model="dialog"
        width="600px"
        style="z-index: 999"
        >
          <v-card>
            <v-card-title>
              <span class="text-h6">使用画像の出典一覧</span>
            </v-card-title>
            <v-divider></v-divider>
            <v-card-text class="mt-5">
              <div v-html="dialogTextWithLineBreaks"></div>
            </v-card-text>
            <v-divider></v-divider>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                :color="selectedTopic.color"
                outlined
                @click="dialog = false"
              >
                CLOSE
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-container>
    </div>
  </template>
  
  
  <script>
  // jsonをインポート
  import topicsJson from "../static/json/normal.json";
  import axios from 'axios';
  
  // 変数
  export default {
    data() {
      return {
        selectedNum: 0, // カルーセルのページ管理
        getTopics: topicsJson.topics, // jsonデータ(現在はここで表示しているが、年齢に応じて変更する場合は別途関数で処理)
        topics: [],
        myfab: false, // menu
        dialog: false, // 出典ダイアログ
        dialogText: "" // ダイアログ内で表示されるテキスト
      };
    },
    // 起動時に実行
    created() {
        // ローカルストレージからデータを読み込む
        const storedData = localStorage.getItem('normalData');
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
      dialogTextWithLineBreaks() {
        // テキストファイルの内容から改行文字をHTMLの改行タグに変換
        return this.dialogText.replace(/\n/g, '<br>');
      }
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
      // ダイアログオープン
      openDialog() {
        // ダイアログが開かれるときにテキストファイルを読み込む
        axios.get(this.selectedTopic.image_path + 'source.txt').then(response => {
          this.dialogText = response.data; // テキストファイルの内容を代入
          this.dialog = true; // ダイアログを表示
        });
      },
    },
  };
  </script>
  
  <style>
  .main {
    background-color: #f5f5f5;
  }

  </style>
  