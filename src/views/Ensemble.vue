<template>
  <v-container>
    <Title en="Ensemble Contest" ja="アンサンブルコンテスト実績" />
    <v-tabs v-model="selectedGeneration" mandatory dense show-arrows>
      <v-tab v-for="generation in generationEnsemble.keys()" :key="generation" :index="generation" :value="generation">
        {{ generation }}s
      </v-tab>
    </v-tabs>
    <v-tabs-items v-model="selectedGeneration">
      <v-tab-item v-for="[generation, ensemble] in generationEnsemble" :key="generation" :index="generation" class="row">
        <Result v-for="(e, index) in ensemble" :key="index" :concours="e" class="col-sm-6 col-12 mb-0 pb-0" />
      </v-tab-item>
    </v-tabs-items>
  </v-container>
</template>

<script>
import Result from '@/components/Result.vue'
import { loadCSV } from '@/lib/csv.js'
import { ENSEMBLE_URL } from '@/config/url.js'
import Title from '@/components/Title.vue'

export default {
  name: 'Ensemble',
  components: {
    Result,
    Title
  },
  data() {
    return {
      generationEnsemble: new Map(),
      selectedGeneration: null,
    }
  },
  created() {
    loadCSV(ENSEMBLE_URL, array => {
      let ensemble = {
        'year': Number(array[0]),
        'music': {
          'title': array[1],
          'subtitle': array[2],
          'composer': array[3],
          'arranger': array[4]
        },
        'concours':[
          {
            'label': '奈良県大会',
            'award': array[6],
            'isRepresent': false
          }
        ]
      };
      if(array[9] !== ''){
        ensemble['concours'][0]['isRepresent'] = true;
        if(ensemble.year === 1972){
          // 1972年は奈良県大会がないので消す
          ensemble['concours'].pop();
        }
        ensemble['concours'].push({
          'label': '関西大会',
          'award': array[9],
          'isRepresent': false,
        });
      }
      return ensemble;
    }).then(res => {
      let m = new Map();
      res.reverse().forEach(e => {
        if(isNaN(e.year)){
          return;
        }
        e.generation = Math.floor(e.year / 10) * 10;
        if(m.get(e.generation) === undefined){
          m.set(e.generation, []);
        }
        m.get(e.generation).push(e);
      });
      this.generationEnsemble = m;
    });
  }
}
</script>

<style scoped>
.v-tab{
  text-transform: none;
}
</style>
