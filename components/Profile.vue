<template>
  <div>
    <v-app-bar dark color="teal darken-1" height="100px" app>
      <v-btn icon
      absolute
      class="ml-3"
      @click="$router.go(-1)"
    >
      <v-icon x-large>mdi-chevron-left</v-icon>
    </v-btn>
      <v-spacer></v-spacer>
      <v-toolbar-title
        class="text-center text-h6 text-sm-h5 font-weight-bold white--text"
        >プロフィール情報の登録</v-toolbar-title
      >
      <v-spacer></v-spacer>
    </v-app-bar>
    <v-container fluid style="max-width: 95%; padding-top: 150px">
    <v-row>
        <v-col
          cols="12"
          md="6"
          v-for="(profile, index) in profiles"
          :key="index"
        >
        <Transition name="fade" appear>
          <v-card height="auto">
            <div class="pa-5">
              <v-card-title class="text-h6 text-sm-h5 font-weight-normal grey--text text--darken-2"
                ><v-icon x-large class="mr-3">mdi-account-circle-outline</v-icon>
                プロフィール情報（{{ index + 1 }}人目）</v-card-title
              >
              <v-card-text>
                <v-form>
                  <v-text-field
                    label="年齢"
                    v-model="profile.age"
                  ></v-text-field>
                  <v-select
                    label="性別"
                    v-model="profile.gender"
                    :items="genders"
                  ></v-select>
                  <!-- 他のプロフィール情報項目を追加 -->
                </v-form>
              </v-card-text>
              <v-card-actions>
                <!-- 3枚目以降のカードには削除ボタンを表示 -->
                <div v-if="index >= 2">
                  <v-btn color="error" @click="removeProfile(index)"
                    >削除</v-btn
                  >
                </div>
                <!-- 最後尾のカードには追加ボタンを表示 -->
                <div v-if="index === profiles.length - 1">
                  <v-btn
                    color="primary"
                    fab
                    dark
                    absolute
                    right
                    @click="addProfile()"
                  >
                    <v-icon>mdi-plus</v-icon>
                  </v-btn>
                </div>
              </v-card-actions>
            </div>
          </v-card>
        </Transition>
        </v-col>
    </v-row>
      <v-row>
        <v-col cols="12">
          <v-btn
            color="primary"
            block
            large
            height="70px"
            class="mt-10 text-h6 text-sm-h5 font-weight-bold white--text"
            @click="submitProfiles()"
          >
            ノスタルジアな話題を見る
          </v-btn>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      profiles: [
        {
          age: "",
          gender: "",
          // 他のプロフィール情報項目を追加
        },
        {
          age: "",
          gender: "",
        },
      ],
      genders: ["男性", "女性", "その他"],
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