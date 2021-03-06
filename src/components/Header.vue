<template>
  <div>
    <v-alert v-for="(news, i) in newsList" :key="i" :index="i" :color="news.bannerType" dense text dismissible border="left">
      <router-link :to="`/news/${news.id}`" class="banner-color">
        {{ news.title }} >>詳細
      </router-link>
    </v-alert>
    <v-app-bar id="header" dense flat short color="secondary lighten-1">
      <router-link to="/">
        <v-img :src="logoImage" max-height="96" max-width="160" contain class="mr-5" />
      </router-link>
      <div class="d-none d-md-flex">
        <v-btn text to="/about" large tile>
          活動について
        </v-btn>
        <v-btn text to="/concerts" large tile>
          演奏会のお知らせ
        </v-btn>
        <v-menu v-model="isOpen" offset-y content-class="pulldown">
          <template v-slot:activator="{ on }">
            <v-btn text large tile v-on="on">
              活動実績
              <v-icon :class="{rotate: isOpen}">
                mdi-chevron-down
              </v-icon>
            </v-btn>
          </template>
          <v-list flat>
            <v-list-item to="/concours/winds">
              <v-list-item-title>吹奏楽コンクール</v-list-item-title>
            </v-list-item>
            <hr class="list-splitter">
            <v-list-item to="/concours/ensemble">
              <v-list-item-title>アンサンブルコンテスト</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
        <v-btn text to="/link" large tile>
          リンク集
        </v-btn>
        <v-btn text to="/contact" large tile>
          お問い合わせ
        </v-btn>
      </div>
      <v-spacer />

      <v-app-bar-nav-icon class="hidden-md-and-up" @click.stop="drawer = !drawer" />
    </v-app-bar>

    <v-navigation-drawer v-model="drawer" right app temporary width="100vw">
      <v-list>
        <v-list-item to="/" @click="drawer = false">
          <v-list-item-title>ホーム</v-list-item-title>
        </v-list-item>
        <v-list-item to="/about" @click="drawer = false">
          <v-list-item-title>活動について</v-list-item-title>
        </v-list-item>
        <v-list-item to="/concerts" @click="drawer = false">
          <v-list-item-title>演奏会のお知らせ</v-list-item-title>
        </v-list-item>
        <v-list-item to="/concours/winds" @click="drawer = false">
          <v-list-item-title>吹奏楽コンクール実績</v-list-item-title>
        </v-list-item>
        <v-list-item to="/concours/ensemble" @click="drawer = false">
          <v-list-item-title>アンサンブルコンテスト実績</v-list-item-title>
        </v-list-item>
        <v-list-item to="/link" @click="drawer = false">
          <v-list-item-title>リンク集</v-list-item-title>
        </v-list-item>
        <v-list-item to="/contact" @click="drawer = false">
          <v-list-item-title>お問い合わせ</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
  </div>
</template>

<script>
import { loadCSV } from '@/lib/csv.js';
import { NEWS_URL } from '@/config/url.js';

export default {
  name: 'Header',
  data: function() {
    return {
      drawer: false,
      logoImage: process.env.BASE_URL + "images/logo.svg",
      isOpen: false,
      newsList: [],
    }
  },
  computed:{
    height: function(){
      return window.innerHeight - 48;
    }
  },
  created(){
    loadCSV(NEWS_URL, array => {
      const date = new Date(array[3]);
      const publishDate = new Date(array[6]);
      const bannerCloseDate = new Date(array[7]);
      const today = new Date();
      return {
        'id': array[0],
        'title': array[1],
        'date': {
          'raw': date,
          'year': date.getFullYear(),
          'month': date.getMonth()+1,
          'day': date.getDate(),
        },
        'publishDate': {
          'raw': publishDate,
          'year': publishDate.getFullYear(),
          'month': publishDate.getMonth()+1,
          'day': publishDate.getDate(),
        },
        'isPublished': publishDate <= today,
        'bannerCloseDate': {
          'raw': bannerCloseDate,
          'year': bannerCloseDate.getFullYear(),
          'month': bannerCloseDate.getMonth()+1,
          'day': bannerCloseDate.getDate(),
        },
        'isBannerClosed': bannerCloseDate <= today,
        'bannerType': array[8],
      }
    }, 1).then(res => {
      const newsList = [];
      res.forEach(news => {
        if(news['isPublished'] && !news['isBannerClosed']){
          newsList.push(news);
        }
      });
      this.newsList = newsList;
    });
  }
}
</script>

<style scoped>
.v-menu__content.pulldown{
  box-shadow: none;
  border: 2px solid var(--v-secondary-base);
}

.list-splitter{
  margin-top: .5rem;
  margin-bottom: .5rem;
}

.rotate{
  transform: rotateZ(180deg);
}

.v-alert{
  margin-bottom: 0;
}

.v-sheet{
  border-radius: 0;
}

.v-alert:not(.v-sheet--tile){
  border-radius: 0;
}

.banner-color{
  color: inherit;
}
</style>
