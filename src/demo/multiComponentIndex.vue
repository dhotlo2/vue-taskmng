<template>
	<div :class="'app'">
		<Topnavigation></Topnavigation>
		<div class="sceneboard">
			<NewScene></NewScene>
		</div>
	</div>
</template>

<script>
	import Topnavigation from './nav.vue';
	import NewScene from './cardscene.vue';
	export default {

	components: { Topnavigation, NewScene },


  	data: function() {

    	return {

    	}
    },
}

</script>

<style>
</style>
<style src="../style.css"/>