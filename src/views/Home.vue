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
						color="#6D2080"
					>
					</v-autocomplete>
				</v-flex>
				<v-flex xs12 sm4>
					<v-autocomplete
						v-model="filtro2"
						v-if="filtered"
						:items="filterList"
						item-text="nome"
						item-value="value"
						:label="filtered"
						style="width: 80%"
						hide-details
						color="#6D2080"
						>
					</v-autocomplete>
				</v-flex>
				<v-flex xs12 sm4>
					<v-btn
					color="#6D2080"
					dark
					@click="[getFlags(filtered, filtro2)]"	
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
          value: "region"
		},
		{
          nome: "Capital",
          value: "capital"
		},
		{
          nome: "Língua",
          value: "lang"
		},
		{
          nome: "País",
          value: "name"
		},
		{
          nome: "Código de ligação",
          value: "callingcode"
		},
	],
	linguas: [],
     filtro2: null,
     filtered: null,
     article: [],
     FlagList: [],
     filterList:[],
	}),
	methods:{

     //Método para pegar apenas os links das bandeiras de cada país
		getFlags(filtered, filtro2){
			if(filtro2 != null){
				this.axios.get(`https://restcountries.eu/rest/v2/${filtered}/${filtro2}?fields=flag`)
				.then((res) => {
					this.FlagList = res.data;
				})
			}else{
				this.axios.get(`https://restcountries.eu/rest/v2/${filtered}?fields=flag`)
				.then((res) => {
					this.FlagList = res.data;
				})
			}
			
		// this.FlagSlice = this.FlagList.slice(0, 12);
		},

		async filter(){
			switch (this.filtered) {
				//filtro por região
				case "region":
					this.axios.get('https://restcountries.eu/rest/v2/all?fields=region')
					.then((res)=> {
						this.filterList = res.data.filter(element => {
							element.nome = element.region
							element.value = element.region
							return element;
						});
					})
				break;
				//filtro por capital
				case "capital":
					this.axios.get('https://restcountries.eu/rest/v2/all?fields=capital')
					.then((res)=>{
						this.filterList = res.data.filter(element => {
							element.nome = element.capital
							element.value = element.capital
							return element;
						});
					})
				break;
				//filtro por língua
				case "lang":
					await this.axios.get('https://restcountries.eu/rest/v2/all?fields=languages')
					.then((res)=>{
						this.linguas = res.data;
					})
					this.filterList = this.linguas.map((lingua)=>{
						for(var i=0; i<lingua.languages.length; i++){
							if(lingua){
								return {
									nome: lingua.languages[i].nativeName,
									value: lingua.languages[i].iso639_1
								}
							}
						}
					})
				break;
				//filtro por país
				case "name":
					this.axios.get('https://restcountries.eu/rest/v2/all?fields=name')
					.then((res)=>{
						this.filterList = res.data.filter(element => {
							element.nome = element.name
							element.value = element.name
							return element;
						})
					})
				break;
				//filtro por codigo de ligação
				case "callingcode":
					await this.axios.get('https://restcountries.eu/rest/v2/all?fields=callingCodes')
					.then((res)=>{
						this.calling = res.data;
					})
					this.filterList = this.calling.map((call)=>{
						for(var i=0; i<call.callingCodes.length; i++){
							if(call){
								return {
									nome: call.callingCodes[i],
									value: call.callingCodes[i]
								}
							}
						}
					})
				break;
				//default país
				default:
					this.axios.get('https://restcountries.eu/rest/v2/all?fields=name')
					.then((res)=>{
						this.filterList = res.data.filter(element => {
							element.nome = element.name
							return element;
						})
					})
				break;
			}
		}
	},
	mounted(){
		this.getFlags("all", null)
	},
}
</script>
