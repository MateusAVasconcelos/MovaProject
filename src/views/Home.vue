<template>
<div>
  <v-container>
    <!-- <v-row>
      <v-flex xs12 sm3>
        <v-select
          :items="items"
          label="Escolha uma opção"
          single-line
          hide-details
          >
        </v-select>
      </v-flex>
      <v-flex xs12 sm3>
        <v-btn
          color="#6D2080"
          dark
        >
          Pesquisar
        </v-btn>
      </v-flex>
    </v-row> -->
    <v-row>
      <v-flex v-for="flag in FlagList" :key="flag.index" sm4 xs12>
        <img width="80%" height="80%" :src="flag.flag"/>
      </v-flex>
    </v-row>
  </v-container>
</div>
</template>
          
<script>
// import ArticleItem from "./ArticleItem"
  export default {
    name: 'Home',
    components: {
      // ArticleItem,
    },
    data: () => ({
      article: [],
      currentPage: 1,
      pageCount: 0,
      FlagList: [],
    }),
    methods:{

      //Método para pegar apenas os links das bandeiras de cada país
      getFlags(){
        this.axios.get('https://restcountries.eu/rest/v2/all?fields=flag')
        .then((res) => {
          this.FlagList = res.data;
        })
        console.log(this.FlagList)
      },
    },
    mounted(){
      this.getFlags()
    },
  }
</script>
