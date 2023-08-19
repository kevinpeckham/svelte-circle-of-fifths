<script lang="ts">
	// components
	import Footer from "$components/Footer.svelte";
	import FooterLink from "$components/FooterLink.svelte";

	// import css
	import "../app.css";
</script>

<template lang="pug">
	slot
	Footer
		//- attribution
		.mb-2
			//- primary attribution
			span
				span.opacity-70 Built by&nbsp;
				FooterLink(href="https://fosstodon.org/@kevinpeckham") Kevin Peckham&nbsp;
				span.opacity-70 in Philadelphia.&nbsp;&nbsp;

			//- secondary attribution
			span
				span.opacity-70 Adapted from&nbsp;
				FooterLink(
					href="https://epiccoleman.com/posts/2023-04-05-svg-circle-of-fifths.html",
					title="go to Blog article"
				)
					| React Circle of Fifths&nbsp;
				span.opacity-70 by Eric Coleman.

		//- copyright and license
		div
			span.opacity-70
				| © 2023 Kevin Peckham.&nbsp;&nbsp;
			FooterLink(
				href="https://github.com/kevinpeckham/svelte-circle-of-fifths/LICENSE"
			) MIT License (FOSS)
			span &nbsp;&nbsp;
			FooterLink(href="https://github.com/kevinpeckham/svelte-circle-of-fifths") Github&nbsp;&nbsp;
</template>
