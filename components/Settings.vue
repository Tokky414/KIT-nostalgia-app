<template>
  <div class="main" style="height: 100%">
    <!-- メニューバー -->
    <v-app-bar dark color="teal darken-1" height="100px" app>
      <v-btn icon absolute class="ml-3" @click="$router.go(-1)">
        <v-icon x-large>mdi-chevron-left</v-icon>
      </v-btn>
      <v-spacer></v-spacer>
      <v-toolbar-title
        class="text-center text-h6 text-sm-h5 font-weight-bold white--text"
        ><v-icon large class="mr-3 mb-1">mdi-cog</v-icon
        >話題の表示設定</v-toolbar-title
      >
      <v-spacer></v-spacer>
    </v-app-bar>
    <!-- 以下、テーブルの表示 -->
    <v-container fluid style="max-width: 95%; padding-top: 120px">
      <v-row>
        <!-- タブ -->
        <v-col cols="12">
          <v-card>
            <v-tabs
              v-model="activeTab"
              color="teal darken-1"
              background-color=""
              class="pa-sm-5"
            >
              <v-tab class="text-sm-h6 font-weight-bold" href="#nostalgia"
                >ノスタルジアな話題</v-tab
              >
              <v-tab class="text-sm-h6 font-weight-bold" href="#normal"
                >通常の話題</v-tab
              >
              <!-- ノスタルジアな話題設定 -->
              <v-tab-item value="nostalgia">
                <div class="pa-3 pa-sm-10">
                  <v-text-field
                    outlined
                    prepend-inner-icon="mdi-magnify"
                    v-model="search"
                    label="トピックを検索"
                  >
                  </v-text-field>
                  <v-data-table
                    :headers="headers"
                    :items="filteredTopicsNostalgia"
                    :search="search"
                    :footer-props="{
                      'items-per-page-options': [10, 20, 50],
                    }"
                  >
                    <template v-slot:[`item.isVisible`]="{ item }">
                      <v-switch
                        :color="item.color"
                        inset
                        v-model="item.isVisible"
                        :disabled="isLastSwitch(item, nostalgia)"
                        @change="saveToLocalStorage(nostalgia)"
                      ></v-switch>
                    </template>
                  </v-data-table>
                  <!-- ローカルストレージをリセットするボタン -->
                  <v-btn
                    @click="resetLocalStorage(nostalgia)"
                    color="error"
                    class="font-weight-bold"
                    >リセット</v-btn
                  >
                  <!-- トピックをランダムにするボタン -->
                  <v-btn
                    @click="randomizeOrder(nostalgia)"
                    color="primary"
                    class="ml-3 font-weight-bold"
                  >
                    ランダム
                  </v-btn>
                </div>
              </v-tab-item>

              <!-- 通常の話題設定 -->
              <v-tab-item value="normal">
                <div class="pa-3 pa-sm-10">
                  <v-text-field
                    outlined
                    prepend-inner-icon="mdi-magnify"
                    v-model="search"
                    label="トピックを検索"
                  >
                  </v-text-field>
                  <v-data-table
                    :headers="headers"
                    :items="filteredTopicsNormal"
                    :search="search"
                    :footer-props="{
                      'items-per-page-options': [10, 20, 50],
                    }"
                  >
                    <template v-slot:[`item.isVisible`]="{ item }">
                      <v-switch
                        :color="item.color"
                        inset
                        v-model="item.isVisible"
                        :disabled="isLastSwitch(item, normal)"
                        @change="saveToLocalStorage(normal)"
                      ></v-switch>
                    </template>
                  </v-data-table>
                  <!-- ローカルストレージをリセットするボタン -->
                  <v-btn
                    @click="resetLocalStorage(normal)"
                    color="error"
                    class="font-weight-bold"
                    >リセット</v-btn
                  >
                  <!-- トピックをランダムにするボタン -->
                  <v-btn
                    @click="randomizeOrder(normal)"
                    color="primary"
                    class="ml-3 font-weight-bold"
                  >
                    ランダム
                  </v-btn>
                </div>
              </v-tab-item>
            </v-tabs>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>
    
<script>
import nostalgiaData from "../static/json/nostalgia.json";
import normalData from "../static/json/normal.json";

export default {
  data() {
    return {
      nosTopics: [],
      norTopics: [],
      nostalgia: 1,
      normal: 2,
      headers: [
        { text: "話題", value: "title", sortable: false },
        { text: "表示・非表示", value: "isVisible" },
      ],
      search: "",
      activeTab: "nostalgia", // 初期表示のタブ
    };
  },
  computed: {
    // 検索時に実行される関数、一致するものを返す
    filteredTopicsNostalgia() {
      if (!this.search) return this.nosTopics;
      const searchLower = this.search.toLowerCase();
      return this.nosTopics.filter((topic) =>
        topic.title.toLowerCase().includes(searchLower)
      );
    },
    filteredTopicsNormal() {
      if (!this.search) return this.norTopics;
      const searchLower = this.search.toLowerCase();
      return this.norTopics.filter((topic) =>
        topic.title.toLowerCase().includes(searchLower)
      );
    },
  },
  created() {
    // ローカルストレージから読み込む
    // nostalgia
    const storedData1 = localStorage.getItem("nostalgiaData");
    // normal
    const storedData2 = localStorage.getItem("normalData");
    // 存在していたらそれを使用する
    if (storedData1) {
      this.nosTopics = JSON.parse(storedData1);
    } else {
      this.nosTopics = nostalgiaData.topics;
    }

    if (storedData2) {
      this.norTopics = JSON.parse(storedData2);
    } else {
      this.norTopics = normalData.topics;
    }
  },
  methods: {
    // ローカルストレージに保存
    // nostalgiaDataという名前で保存
    saveToLocalStorage(type) {
      if (type == this.nostalgia) {
        localStorage.setItem("nostalgiaData", JSON.stringify(this.nosTopics));
      } else {
        localStorage.setItem("normalData", JSON.stringify(this.norTopics));
      }
    },
    // ローカルストレージを削除
    resetLocalStorage(type) {
      if (type == this.nostalgia) {
        localStorage.removeItem("nostalgiaData");
        this.nosTopics = nostalgiaData.topics;
        // トグルスイッチの見た目をリセット(ページをリロード)
        location.reload();
      } else {
        localStorage.removeItem("normalData");
        this.norTopics = normalData.topics;
        // トグルスイッチの見た目をリセット(ページをリロード)
        location.reload();
      }
    },
    // トピックの順番をランダムにする
    randomizeOrder(type) {
      if (type == this.nostalgia) {
        this.nosTopics = this.nosTopics
          .map((topic) => ({ ...topic })) // Create a new array with topic copies
          .sort(() => Math.random() - 0.5); // Shuffle the topics
        this.saveToLocalStorage(this.nostalgia);
      } else {
        this.norTopics = this.norTopics
          .map((topic) => ({ ...topic })) // Create a new array with topic copies
          .sort(() => Math.random() - 0.5); // Shuffle the topics
        this.saveToLocalStorage(this.normal);
      }
    },

    // 最後のスイッチは無効化
    isLastSwitch(topic, type) {
      if (type == this.nostalgia) {
        // Check if this switch is the last one that is on
        return (
          this.nosTopics.filter((item) => item.isVisible).length === 1 &&
          topic.isVisible
        );
      } else {
        // Check if this switch is the last one that is on
        return (
          this.norTopics.filter((item) => item.isVisible).length === 1 &&
          topic.isVisible
        );
      }
    },
  },
};
</script>

<style scoped>
.main {
  background-color: #f5f5f5;
}
</style>