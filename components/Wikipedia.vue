<template>
  <div>
    <v-app-bar dark color="teal" height="100px" app>
      <v-spacer></v-spacer>
      <v-toolbar-title
        class="text-center text-h6 text-sm-h5 font-weight-bold white--text"
        >Wikipedia記事検索</v-toolbar-title
      >
      <v-spacer></v-spacer>
    </v-app-bar>

    <v-container fluid style="max-width: 95%; padding-top: 150px">
      <v-row>
        <v-col cols="12" md="6">
          <v-card height="auto" transition-swing>
            <div class="pa-5">
              <v-card-title class="text-h6 text-sm-h5 font-weight-boldt"
                >検索したい単語を入力</v-card-title
              >
              <v-card-text>
                  <v-text-field
                    v-model="searchKeyword"
                    label="キーワードを入力して検索"
                    @keyup.enter="searchArticles()"
                  ></v-text-field>
              </v-card-text>
              <v-card-actions>
                <v-btn color="primary" @click="searchArticles()">検索</v-btn>
              </v-card-actions>
            </div>
          </v-card>
        </v-col>
      </v-row>

      <div v-if="articles.length > 0">
        <v-row>
          <v-col cols="12" md="6">
            <v-card height="auto" transition-swing>
              <div class="pa-5"
                    v-for="(article, index) in articles"
                    :key="index">
                <v-card-title class="text-h6 text-sm-h5 font-weight-boldt"
                  >検索結果</v-card-title
                >
                <v-card-text>
                  <v-list>
                    <v-list-item>
                      <v-list-item-content>
                        <v-list-item-title>{{
                          article.title
                        }}</v-list-item-title>
                        <!-- スタイルをカスタマイズしてテキストの切り捨てを無効にする -->
                        <v-list-item-subtitle
                          class="v-list-item__subtitle--no-truncate"
                        >
                          {{ article.extract }}
                        </v-list-item-subtitle>
                      </v-list-item-content>
                      <v-list-item-action>
                        <img
                          :src="article.thumbnail"
                          v-if="article.thumbnail"
                          alt="記事の画像"
                        />
                      </v-list-item-action>
                      <div v-if="relatedImages.length > 0">
                        <h2>関連する画像</h2>
                        <div v-for="(image, index) in relatedImages" :key="index">
                          <img :src="image" alt="関連する画像" />
                        </div>
                      </div>
                    </v-list-item>
                  </v-list>
                </v-card-text>
                <v-card-actions>
                  <v-btn color="primary" :href="article.link">Wikipediaのリンク</v-btn>
                </v-card-actions>
              </div>
            </v-card>
          </v-col>
        </v-row>
      </div>
    </v-container>
  </div>
</template>
  
  <script>
export default {
  data() {
    return {
      searchKeyword: "", // 検索キーワード
      articles: [], // 検索結果の記事リスト
      relatedImages: [], // 関連する画像リスト
    };
  },
  methods: {
    async searchArticles() {
      this.relatedImages = []; // 関連する画像リストをクリア
      // 検索結果を取得する処理は省略
      // ...
      // 検索結果があれば、記事を表示
      try {
        const response = await this.$axios.get(
          `https://ja.wikipedia.org/api/rest_v1/page/summary/${this.searchKeyword}`
        );
        console.log(response);
        if (response.data.title) {
          const article = {
            title: response.data.title,
            extract: response.data.extract,
            link: response.data.content_urls.mobile.page,
            thumbnail: response.data.thumbnail
              ? response.data.thumbnail.source
              : null,
          };
          this.articles = [article]; // 検索結果を上書き
        } else {
          // 検索結果がない場合はクリア
          this.articles = [];
        }
      } catch (error) {
        console.error(
          `「${this.searchKeyword}」の記事の取得中にエラーが発生しました:`,
          error
        );
      }
    },
    async fetchRelatedImages() {
      try {
        // 別の画像検索エンジンやAPIを使用して、キーワードに関連する画像を取得
        // 例: Google Images APIを利用して画像を取得
        const response = await this.$axios.get(
          `https://api.example.com/image-search?q=${this.searchKeyword}`
        );

        if (response.data && response.data.images) {
          this.relatedImages = response.data.images;
        }
      } catch (error) {
        console.error("関連する画像の取得中にエラーが発生しました:", error);
      }
    },
  },
};
</script>
  
  <style scoped>
/* テキストの切り捨てを無効にするスタイル */
.v-list-item__subtitle--no-truncate {
  white-space: normal;
  overflow: visible;
}
</style>
  