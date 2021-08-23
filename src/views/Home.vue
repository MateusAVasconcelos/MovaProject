<template>
<div>
  <v-container>
    <v-row class="pl-1 pt-7 pb-7">
      <v-flex xs12 sm4>
        <v-autocomplete
          v-model="filtered"
          :items="Filtros"
          label="Escolha uma opção"
          item-text="nome"
          item-value="value"
          @change="filter($event)"
          hide-details
          clearable
          style="width: 80%"
          >
        </v-autocomplete>
      </v-flex>
      <v-flex xs12 sm4>
        <v-autocomplete
          v-if="filtered"
          :items="filterList"
          item-text = "nome"
          item-value = "nome"
          label= "temporario"
          style="width: 80%"
          @change="getFlags($event)"
          hide-details
        >
        </v-autocomplete>
      </v-flex>
      <v-flex xs12 sm4>
        <v-btn
          color="#6D2080"
          dark
        >
          Pesquisar
        </v-btn>
      </v-flex>
    </v-row>
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
      Filtros: [
        {
          nome: "Região",
          value: "1"
        },
        {
          nome: "Capital",
          value: "2"
        },
        {
          nome: "Língua",
          value: "3"
        },
        {
          nome: "País",
          value: "4"
        },
        {
          nome: "Código de ligação",
          value: "5"
        },
      ],
      linguas: [],
      filtered: null,
      article: [],
      FlagList: [],
      filterList:[],
    }),
    methods:{

      //Método para pegar apenas os links das bandeiras de cada país
      getFlags(){
        this.axios.get('https://restcountries.eu/rest/v2/all?fields=flag')
        .then((res) => {
          this.FlagList = res.data;
        })
        // this.FlagSlice = this.FlagList.slice(0, 12);
      },

      filter(){
        switch (this.filtered) {
          case "1":
            this.axios.get('https://restcountries.eu/rest/v2/all?fields=region')
            .then((res)=> {
              this.filterList = res.data.filter(element => {
                element.nome = element.region
                return element;
              });
            })
            break;
          case "2":
            this.axios.get('https://restcountries.eu/rest/v2/all?fields=capital')
            .then((res)=>{
              this.filterList = res.data.filter(element => {
                element.nome = element.capital
                return element;
              });
            })
            break;
          case "3":
            this.axios.get('https://restcountries.eu/rest/v2/all?fields=languages')
            .then((res)=>{
              this.linguas = res.data;
            })
            this.filterList = this.linguas.map((lingua)=>{
              for(var i=0;i<lingua.languages.lenght;i++){
                console.log(lingua.languages.lenght)
                if(lingua){
                  return {
                    id: lingua.languages[i].iso639_1,
                    nome: lingua.languages.nativeName
                  }
                }
              }
            })
            console.log(this.filterList.length)
            break;
          default:
            this.axios.get('https://restcountries.eu/rest/v2/all?fields=name')
            .then((res)=>{
              this.filterList = res.data.filter(element => {
                element.nome = element.name
                return element;
              })
            })
        }
      }
    },
    mounted(){
      this.getFlags()
    },
  }
</script>
