<template>
</template>

<script>
</script>

<style lang="less">
	.allPanel{
		min-width: 740px;
		position: fixed;
		font-size: 1.7rem;
		position: absolute;
		@width:50%;width:@width;left: calc(100% - @width * 1.5);
		top: 22%;
		border-radius: 15px;
		background: rgba(199, 199, 199,.9);
		backdrop-filter: blur(2px);
		padding: 1em 2em;
		line-height: 1.5em;
		box-sizing: border-box;
		height: 50vh;
		text-align: left;
		animation: scale reverse .3s both;
		z-index: 10;
		.btn{
			cursor: pointer;
			border-radius: 5px;
			display: inline-block;
			background: #b4b4b4;
			padding: .25em 1em;
			transition: .25s;
			&:hover{
				filter: brightness(90%);
				transform: scale(1.1);
				z-index: 10;
			}
		}
		.close{
			position: fixed;bottom: 5%;left: 40%;
			--c1:red;--c2:white;
			display: block;
			background: var(--c1);
			padding: .25em 1em;
			border-radius: 5px;
			color: var(--c2);
		}
	}
</style>
