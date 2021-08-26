<template>
<div>
		<v-container v-show="!viewPais">
			<v-row class="pl-1 pt-7 pb-7">
				<v-flex xs12 sm4>	
					<v-autocomplete
						v-model="filtered"
						:items="Filtros"
						label="Escolha uma opção"
						item-text="nome"
						item-value="value"
						@change="[filtro2 = null, filter($event)]"
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
				<v-flex v-for="flag in paginaFlagList" :key="flag.index" sm4 xs12>
					<img width="80%" height="80%" :src="flag.flag" @click="informations(flag.alpha2Code)"/>
				</v-flex>
			</v-row>
			<v-pagination
				v-model="page"
				:length="Math.ceil(FlagList.length/perPage)"
				@input="visiblePages($event)"
				class="pt-4"
				color="purple"
			>
			</v-pagination>
		</v-container>
		<v-container v-show="viewPais" grid-list-xl class="mt-md-12">
			<v-layout row wrap>
				<v-flex d-flex xs12 sm6 md5>
					<v-card elevation="0">
						<v-img :src="pais.flag" height="100%"> </v-img>
					</v-card>
				</v-flex>
				<v-flex d-flex xs12 sm6 md7>
					<v-flex d-flex>
						<v-layout row wrap>
							<v-card elevation="0">
								<v-card-subtitle>
									Nome: {{pais.name}}
								</v-card-subtitle>
								<v-card-subtitle>
									Capital: {{pais.capital}}
								</v-card-subtitle>
								<v-card-subtitle>
									Região: 
									<span
										class="linkClickable"
										@click="[filtered='region', filtro2 = pais.region, viewPais = false,filter(), getFlags(filtered, filtro2)]"
									> 
										{{pais.region}} 
									</span>
								</v-card-subtitle>
								<v-card-subtitle>
									Sub-região: {{pais.subregion}}
								</v-card-subtitle>
								<v-card-subtitle>
									População: {{pais.population}}
								</v-card-subtitle>
								<v-card-subtitle>
									Línguas: 
									<span v-for="language in pais.languages" :key="language.index">{{language.nativeName}}, </span>
								</v-card-subtitle>
							</v-card>
						</v-layout>
					</v-flex>
				</v-flex>
			</v-layout>
			<v-layout>
				<v-card elevation="0" class="mt-md-12">
					<v-card-text v-if="paisesVizinhos.length>0">
						Países vizinhos
					</v-card-text>
					<v-card-text v-else>
						Esse país não possui países vizinhos
					</v-card-text>
				</v-card>
			</v-layout>
			<v-layout row wrap>
				<v-flex d-flex>
					<v-layout wrap>
						<v-flex md4 sm4 v-for="vizinho in paisesVizinhos" :key="vizinho.id">
							<v-hover v-slot="{ hover }">
								<v-card class="card-container">
									<v-img 
										:class="{ 'on-hover': hover }" 
										:src="vizinho.flag" 
										height="200px" 
										@click="[informations(vizinho.alpha2Code)]"
									>
									</v-img>
								</v-card>
							</v-hover>
						</v-flex>
					</v-layout> 
				</v-flex>
			</v-layout>
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
	pais:[],
	paisesVizinhos: [],
	paginaVizinhos: [],
	viewPais: false,
	linguas: [],
	page: 1,
	perPage: 12,
    filtro2: null,
    filtered: null,
    article: [],
    FlagList: [],
	paginaFlagList: [],
    filterList:[],
	}),
	methods:{

     //Método para pegar apenas os links das bandeiras de cada país
		getFlags(filtered, filtro2){
			if(filtro2 != null){
				this.axios.get(`https://restcountries.eu/rest/v2/${filtered}/${filtro2}?fields=flag;alpha2Code`)
				.then((res) => {
					this.FlagList = res.data.map(element => {
						return {
							flag: element.flag,
							alpha2Code: element.alpha2Code
						}
					});
					this.paginaFlagList = res.data.slice((this.page - 1)* this.perPage, this.page* this.perPage)
				})
			}else{
				this.axios.get(`https://restcountries.eu/rest/v2/${filtered}?fields=flag;alpha2Code`)
				.then((res) => {
					this.FlagList = res.data.map(element => {
						return {
							flag: element.flag,
							alpha2Code: element.alpha2Code
						}
					});
					this.paginaFlagList = res.data.slice((this.page - 1)* this.perPage, this.page* this.perPage)
				})
			}
		},

		async informations(alpha2Code){
			this.paisesVizinhos = [],
			await this.axios.get(`https://restcountries.eu/rest/v2/alpha/${alpha2Code}?fields=name;flag;region;capital;languages;alpha2Code;population;subregion;borders`)
			.then((res)=>{
				this.pais = res.data
				for(var vizinhos = 0; vizinhos<res.data.borders.length; vizinhos++){
					this.axios.get(`https://restcountries.eu/rest/v2/alpha/${res.data.borders[vizinhos]}?fields=flag;alpha2Code`)
					.then((response)=>{
						this.paisesVizinhos.push({
							alpha2Code: response.data.alpha2Code,
							flag: response.data.flag
						})
					})
				}
			})
			console.log(this.paisesVizinhos)
			this.viewPais = true
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
		},
		visiblePages(){
			this.paginaFlagList = this.FlagList.slice((this.page - 1)* this.perPage, this.page* this.perPage)
		},
		
	},
	mounted(){
		this.getFlags("all", null)
	},
}
</script>
<style scoped>
.linkClickable {
	color: purple;
	text-decoration: underline;
	text-decoration-color: purple;
}
</style>
