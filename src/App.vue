<template>
  <v-app :dark="true">
    <div id="app">
      <Header />
      <v-content>
        <router-view />
      </v-content>

      <Footer />
    </div>
  </v-app>
</template>

<script>
import Header from '@/components/Header.vue'
import Footer from '@/components/Footer.vue'

export default {
  name: 'App',
  components: {
    Header,
    Footer
  },
  watch: {
    '$route' (routeInstance) {
      this.setTitle(routeInstance);
    }
  },
  mounted : function(){
    var routeInstance = this.$route;
    this.setTitle(routeInstance);
  },
  methods: {
    setTitle : function(routeInstance){
      // titleのセット
      var title = '';
      if(routeInstance.name){
        title = routeInstance.name + ' | ';
      }
      title += '奈良高専吹奏楽部';
      document.title = title;
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Aleo:wght@400;700&display=swap');

*{
  font-family: 'Inter', 'Noto Sans JP', sans-serif;
}

::selection{
  background-color: #0e177f!important;
  color: #fff!important;
}

.serif{
  font-family: 'Aleo', 'Noto Serif JP', sans-serif;
}

.v-toolbar__content a:hover, a.v-list-item:hover {
  text-decoration: none;
}

.centering{
  margin: 0 auto;
}

.header-jp{
  letter-spacing: 4px;
}

.bg-none{
  background-color: rgba(0,0,0,0);
}</style>
