<template>
	<div id="app">
		<panel></panel>
	</div>
</template>

<script>
import panel from './components/panel.vue'

export default {
  name: 'App',
  components: {
	  panel
  }
}
</script>

<style lang="less">
	*{margin: 0;padding: 0;}
	#app {
		width: 100%;height: 100vh;
		min-width: 900px;
		font-family: "微软雅黑",Avenir;
		overflow: hidden;
	}
</style>
