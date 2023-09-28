<template>
    <div>
      <v-app-bar dark color="teal" height="100px" app fixed>
        <v-spacer></v-spacer>
        <v-toolbar-title
          class="text-center text-h6 text-sm-h5 font-weight-bold white--text"
          >ホームメニュー</v-toolbar-title
        >
        <v-spacer></v-spacer>
      </v-app-bar>
  
      <v-container fluid style="max-width: 95%; padding-top: 150px">
      <v-row>
          <v-col
            cols="12"
            md="6"
            v-for="(menu, index) in menus"
            :key="index"
          >
            <v-card 
            height="auto"
            class="ma-5"
            link
            shaped
            :to="menu.link"
            nuxt>
              <div class="pa-5">
                <v-card-title class="text-h6 text-sm-h5 font-weight-black grey--text text--darken-2"
                  ><v-icon x-large class="mr-5">{{ menu.icon }}</v-icon>{{ menu.title }}</v-card-title
                >
                <v-card-subtitle class="mt-1 font-weight-black grey--text text--darken-2">
                    {{ menu.explain }}
                </v-card-subtitle>
              </div>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        menus: [
         {
            title: "ノスタルジアな話題",
            explain: "懐かしさを想起させる話題を提供します",
            icon:"mdi-account-voice",
            link:"/topic"
          },
          {
            title: "通常の話題",
            explain: "普段における通常の話題を提供します",
            icon:"mdi-account-voice",
            link:"/normal"
          },
          {
            title: "パーソナライズされたノスタルジアな話題",
            explain: "懐かしさを想起させる話題を年齢と性別から分析して提供します（未実装）",
            icon:"mdi-account-voice",
            link:"/profile"
          },
          {
            title: "話題の表示設定",
            explain: "表示する話題の設定を行います（ローカルストレージを使用）",
            icon:"mdi-cog",
            link:"/settings"
          },
          
        ]
      };
    },
    methods: {
      addProfile() {
        // 新しいプロフィールエントリを追加
        const newIndex = this.profiles.length + 1;
        this.profiles.push({
          age: "",
          gender: "",
          // 他のプロフィール情報項目を追加
        });
  
        // DOMの更新が完了した後にスクロールを行う
        this.$nextTick(() => {
          this.$vuetify.goTo(9000, {
            duration: 1000, // スクロールのアニメーション時間（ミリ秒）
            offset: 0, // スクロール位置のオフセット（ピクセル）
            easing: "easeInOutCubic", // イージング関数
          });
        });
      },
      removeProfile(index) {
        // プロフィールエントリを削除
        this.profiles.splice(index, 1);
      },
      submitProfiles() {
        // プロフィール情報を一括で送信するロジックを追加
        //console.log('Send-OK', this.profiles);
  
        // データをsessionStorageに保存
        // jsonにすることで一括で管理
        sessionStorage.setItem("profilesJson", JSON.stringify(this.profiles));
  
        // フォームデータを次のページに送信して遷移
        this.$router.push({
          name: "topic",
        });
      },
    },
  };
  </script>
  
  <style scoped>
  /* CSSファイルまたは<style>セクション内で */
  .fade-enter-active, .fade-leave-active {
      transition: opacity 0.5s;
  }
  .fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
    opacity: 0;
  }
  </style>