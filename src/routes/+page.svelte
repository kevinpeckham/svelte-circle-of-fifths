<!-- Example Svelte Page / Starter Web Page-->
<script lang="ts">
	// import contextApi functions from Svelte
	import { setContext } from "svelte";

	// import store functions from svelte/store
	import { writable } from "svelte/store";

	// components
	import CircleOfFifths from "$components/CircleOfFifths.svelte";
	import ButtonLink from "$components/ButtonLink.svelte";

	// create local store
	const activeChordButton = writable("-");

	// add store to context
	setContext("activeChordButtonStore", activeChordButton);

	// types
	import type { LayoutData } from "./$types";

	// catch data from layout function in +layout.ts
	export let data: LayoutData;
</script>

<template lang="pug">
	//- head
	svelte:head
		title Circle of Fifths - Beta
		meta(
			content="An interactive Circle of Fifths component for svelte.",
			name="description"
		)

	//- body
	main.relative.grid.grid-cols-1.min-h-screen.place-items-center.p-4.text-neutral-50
		//- headline
		.absolute.top-8.left-8
			h1.font-medium.text-24.mb-1 Svelte Circle of Fifths
			.opacity-80.mb-4 A Circle of Fifths component for svelte.
			ButtonLink(
				class="mt-4",
				href="https://github.com/kevinpeckham/svelte-circle-of-fifths"
			) Github

		//- outer circle container
		.flex.items-center.justify-center.w-auto.aspect-square(class="h-[98vmin]")
			CircleOfFifths
</template>
