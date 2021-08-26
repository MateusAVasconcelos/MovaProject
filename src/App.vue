<template>
	<v-app>
		<v-app-bar
			app
			color="white"
			dark
	>
    <div class="d-flex align-center">
		<v-img
			alt="Mova Logo"
			class="shrink mr-2"
			contain
			src="https://mova.vc/img/footer_mova.svg"
			transition="scale-transition"
			width="100"
		/>
		</div>
		<v-spacer></v-spacer>
		<v-btn
			target="_blank"
			color="#6D2080"
			outlined
		>
			<v-icon>mdi-keyboard-return</v-icon>
			<span class="mr-2 pl-2 ">Voltar</span>
		</v-btn>
		</v-app-bar>

		<v-main>
			<router-view/>
		</v-main>
	</v-app>
</template>

<script>
export default {
	name: 'App',
	components: {
	},
	data: () => ({
	}),
};
</script>
