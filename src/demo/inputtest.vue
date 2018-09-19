<template>
	<div :class="'app'">
		<input class="cardinput" type="text" v-model="card.data"
                          @keyup.enter="$event.target.blur()"
                          @dblclick="$event.target.select()"
                          @mousedown.self.prevent
                        >{{test}}
	</div>

	
</template>

<script>

import Card from './index';

export default {
	name: "inputtest",

	data: function(){
		return {
			 test: ''
		}

	}
}

	
</script>

<style src="../style.css"/>