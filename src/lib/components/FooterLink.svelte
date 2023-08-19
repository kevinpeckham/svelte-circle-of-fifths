<!--
@component
Simple link component for footer
- prop href: string -- link to website
- prop label: string -- link text
-->

<script lang="ts">
	//- props
	export let href: string | null = null;
</script>

<template lang="pug">
	a.inline-block.underline-offset-4.opacity-80.transition-all(
		class="hover:text-accent underline decoration-neutral-100/40 hover:opacity-100",
		href!="{ href }"
	)
		slot
</template>
