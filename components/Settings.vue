<template>
    <div>
      <!-- メニューバー -->
      <v-app-bar dark color="teal" height="100px" app>
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
      <v-container fluid style="max-width: 95%; padding-top: 150px">
        <v-row>
          <!-- タブ -->
          <v-col cols="12">
            <v-card>
              <v-tabs
                v-model="activeTab"
                color="teal"
                background-color=""
                class="pa-5"
              >
                <v-tab class="text-h6 font-weight-bold" href="#nostalgia"
                  >ノスタルジアな話題</v-tab
                >
                <v-tab class="text-h6 font-weight-bold" href="#normal"
                  >通常の話題</v-tab
                >
                <!-- ノスタルジアな話題設定 -->
                <v-tab-item value="nostalgia">
                  <div class="pa-10">
                    <v-text-field
                      outlined
                      prepend-inner-icon="mdi-magnify"
                      v-model="search"
                      label="トピックを検索"
                    >
                    </v-text-field>
                    <v-data-table
                      :headers="headers"
                      :items="filteredTopics"
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
                          :disabled="isLastSwitch(item)"
                          @change="saveToLocalStorage()"
                        ></v-switch>
                      </template>
                    </v-data-table>
                    <!-- ローカルストレージをリセットするボタン -->
                    <v-btn
                      @click="resetLocalStorage()"
                      color="error"
                      class="font-weight-bold"
                      >リセット</v-btn
                    >
                    <!-- トピックをランダムにするボタン -->
                    <v-btn
                      @click="randomizeOrder()"
                      color="primary"
                      class="ml-3 font-weight-bold"
                    >
                      ランダム
                    </v-btn>
                  </div>
                </v-tab-item>
  
                <!-- 通常の話題設定 -->
                <v-tab-item value="normal">
                  <div class="pa-10">
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
                          :disabled="isLastSwitchNormal(item)"
                          @change="saveToLocalStorageNormal()"
                        ></v-switch>
                      </template>
                    </v-data-table>
                    <!-- ローカルストレージをリセットするボタン -->
                    <v-btn
                      @click="resetLocalStorageNormal()"
                      color="error"
                      class="font-weight-bold"
                      >リセット</v-btn
                    >
                    <!-- トピックをランダムにするボタン -->
                    <v-btn
                      @click="randomizeOrder()"
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
  import nostalgiaData from "../static/json/temp.json";
  import normalData from "../static/json/normal.json";
  
  export default {
    data() {
      return {
        topics: [],
        normals: [],
        headers: [
          { text: "話題", value: "title", sortable: false },
          { text: "表示・非表示", value: "isVisible" },
        ],
        search: "",
        activeTab: "nostalgia", // 初期表示のタブ
      };
    },
    computed: {
      filteredTopics() {
        if (!this.search) return this.topics;
        const searchLower = this.search.toLowerCase();
        return this.topics.filter((topic) =>
          topic.title.toLowerCase().includes(searchLower)
        );
      },
      filteredTopicsNormal() {
        if (!this.search) return this.normals;
        const searchLower = this.search.toLowerCase();
        return this.normals.filter((topic) =>
          topic.title.toLowerCase().includes(searchLower)
        );
      },
    },
    created() {
      const storedData1 = localStorage.getItem("nostalgiaData");
      const storedData2 = localStorage.getItem("normalsData");
      if (storedData1) {
        this.topics = JSON.parse(storedData1);
      } else {
        this.topics = nostalgiaData.topics;
      }
  
      if (storedData2) {
        this.normals = JSON.parse(storedData2);
      } else {
        this.normals = normalData.topics;
      }
    },
    methods: {
      // ローカルストレージに保存
      // nostalgiaDataという名前で保存
      saveToLocalStorage() {
        localStorage.setItem("nostalgiaData", JSON.stringify(this.topics));
      },
      // ローカルストレージを削除
      resetLocalStorage() {
        localStorage.removeItem("nostalgiaData");
        this.topics = nostalgiaData.topics;
        // トグルスイッチの見た目をリセット(ページをリロード)
        location.reload();
      },
      // normalsDataという名前で保存
      saveToLocalStorageNormal() {
        localStorage.setItem("normalsData", JSON.stringify(this.normals));
      },
      // ローカルストレージを削除
      resetLocalStorageNormal() {
        localStorage.removeItem("normalsData");
        this.normals = normalData.topics;
        // トグルスイッチの見た目をリセット(ページをリロード)
        location.reload();
      },
      randomizeOrder() {
        this.topics = this.topics
          .map((topic) => ({ ...topic })) // Create a new array with topic copies
          .sort(() => Math.random() - 0.5); // Shuffle the topics
        this.saveToLocalStorage();
      },
      // 最後のスイッチは無効化
      isLastSwitch(topic) {
        // Check if this switch is the last one that is on
        return (
          this.topics.filter((item) => item.isVisible).length === 1 &&
          topic.isVisible
        );
      },
      // 最後のスイッチは無効化
      isLastSwitchNormal(topic) {
        // Check if this switch is the last one that is on
        return (
          this.normals.filter((item) => item.isVisible).length === 1 &&
          topic.isVisible
        );
      },
    },
  };
  </script>
    