<!--
@component
SVG Circle of Fifths
- This component renders a circle of fifths svg element
- It borrows heavily from the work of Eric Coleman
- See his blog post https://epiccoleman.com/posts/2023-04-05-svg-circle-of-fifths.html
- And his github repo https://github.com/epiccoleman/react-circle-of-fifths/tree/main
- It also borrows functions from Håken Lid
- see: https://observablehq.com/@haakenlid/svg-circle
- Also, see the circle of fifths wikipedia page https://en.wikipedia.org/wiki/Circle_of_fifths
-->

<script lang="ts">
	//- data
	import { default as data } from "$data/circle-of-fifths-data.json";
	const chordData: ChordDatum[] = data;

	//- types
	class ChordDatum {
		[key: string]: string;
		displayMajor: string;
		idMajor: string;
		displayRelativeMinor: string;
		idMinor: string;
		displayDiminished: string;
		idDiminished: string;
		keySignature: string;
		constructor() {
			this.displayMajor = "";
			this.idMajor = "";
			this.displayRelativeMinor = "";
			this.idMinor = "";
			this.displayDiminished = "";
			this.idDiminished = "";
			this.keySignature = "";
		}
	}

	//- import store function
	import { writable } from "svelte/store";

	//-- create store
	const emptyDatum = new ChordDatum();
	const activeChord = writable("");

	//- variables
	const n = data.length; // number of wedges - 12

	// functions written by Håken Lid
	function segmentPath(
		x: number,
		y: number,
		r0: number,
		r1: number,
		d0: number,
		d1: number,
	): string {
		// https://svgwg.org/specs/paths/#PathDataEllipticalArcCommands
		const arc = Math.abs(d0 - d1) > 180 ? 1 : 0;
		const point = (radius: number, degree: number) =>
			polarToCartesian(x, y, radius, degree)
				.map((n) => n.toPrecision(5))
				.join(",");
		return [
			`M${point(r0, d0)}`,
			`A${r0},${r0},0,${arc},1,${point(r0, d1)}`,
			`L${point(r1, d1)}`,
			`A${r1},${r1},0,${arc},0,${point(r1, d0)}`,
			"Z",
		].join("");
	}
	function polarToCartesian(x: number, y: number, r: number, degrees: number) {
		const radians = (degrees * Math.PI) / 180.0;
		return [x + r * Math.cos(radians), y + r * Math.sin(radians)];
	}

	// custom helper functions
	function wedgePath(r0: number, r1: number, i: number) {
		return segmentPath(200, 200, r0, r1, (360 / n) * i, ((i + 1) * 360) / n);
	}
	function textCoords(r: number, i: number) {
		const [x, y] = polarToCartesian(200, 200, r, (360 / n) * i);
		return [x, y];
	}

	//- interaction functions
	function onMouseEnter(event: PointerEvent) {
		const target = event.target as SVGPathElement;
		const index = Number(target.dataset.index) ?? 0;
		const adjustedIndex = index == 11 ? 0 : index + 1;
		const datum = data[adjustedIndex];
		const mode = target.dataset.mode ?? "";

		if (mode === "major") {
			const chord = datum.displayMajor;
			$activeChord = chord + " maj" ?? "";
		} else if (mode === "minor") {
			const chord = datum.displayRelativeMinor;
			const chordAdjusted = chord.replace("m", " min");
			$activeChord = chordAdjusted ?? "";
		} else if (mode === "diminished") {
			const chord = datum.displayDiminished;
			const chordAdjusted = chord.replace("°", " dim");
			$activeChord = chordAdjusted ?? "";
		}
	}
	function onMouseLeave(event: PointerEvent) {
		$activeChord = "";
	}
</script>

<template lang="pug">
	svg#circle-of-fifths.w-full.h-auto.aspect-square(
		height="800",
		viewBox="0 0 400 400",
		width="800",
		xmlns="http://www.w3.org/2000/svg"
	)
		g(
			class="rotate-[15deg]",
			style="transform-origin: 200px 200px 0px"
		)
			+each('data as item, i')
				//- major ring
				path(
					class="fill-accent stroke-primary stroke-[0.1em] transition-opacity hover:opacity-60",
					aria-labelledby="chord-button-label-{item.idMajor.toLowerCase()}",
					d!="{ wedgePath(180, 120, i) }",
					data-index!="{ i }",
					data-mode="major",
					id!="chord-button-{item.idMajor}",
					on:mouseenter!="{ onMouseEnter }",
					on:mouseleave!="{ onMouseLeave }",
					role="button"
				)

				//- minor ring
				path(
					class="fill-accent/90 stroke-primary stroke-[0.1em] transition-opacity hover:opacity-60",
					aria-labelledby="chord-button-label-{item.idMinor.toLowerCase()}",
					d!="{ wedgePath(120, 80, i) }",
					data-index!="{ i }",
					data-mode="minor",
					id!="chord-button-{item.idMinor.toLowerCase()}",
					on:mouseenter!="{ onMouseEnter }",
					on:mouseleave!="{ onMouseLeave }",
					role="button"
				)

				//- diminished ring
				path(
					class="fill-accent/80 stroke-primary stroke-[0.1em] transition-opacity hover:opacity-60",
					aria-labelledby="chord-button-label-{item.idDiminished.toLowerCase()}",
					d!="{ wedgePath(80, 50, i) }",
					data-index!="{ i }",
					data-mode="diminished",
					id!="chord-button-{item.idDiminished.toLowerCase()}",
					on:mouseenter!="{ onMouseEnter }",
					on:mouseleave!="{ onMouseLeave }"
				)

		//- text labels
		g.pointer-events-none
			+each('data as item, i')
				//- major labels
				+const('radius1 = 150')
				+const('[x1, y1] = textCoords(radius1, i)')
				text(
					id!="chord-button-label-{item.idMajor.toLowerCase()}",
					style="text-anchor: middle; dominant-baseline: central",
					x!="{ x1.toFixed(2) }",
					y!="{ y1.toFixed(2) }"
				) { item.displayMajor }

				//- minor labels
				+const('radius2 = 100')
				+const('[x2, y2] = textCoords(radius2, i)')
				text(
					class="text-[0.8em]",
					id!="chord-button-label-{item.idMinor}.toLowerCase()",
					style="text-anchor: middle; dominant-baseline: central",
					x!="{ x2.toFixed(2) }",
					y!="{ y2.toFixed(2) }"
				) { item.displayRelativeMinor }

				//- inner labels
				+const('radius3 = 65')
				+const('[x3, y3] = textCoords(radius3, i)')
				text(
					class="text-[0.6em]",
					id!="chord-button-label-{item.idDiminished.toLowerCase()}",
					style="text-anchor: middle; dominant-baseline: central",
					x!="{ x3.toFixed(2) }",
					y!="{ y3.toFixed(2) }"
				) { item.displayDiminished }

		//- center text
		g.pointer-events-none
			text.fill-accent(
				class="text-[.8em]",
				style="text-anchor: middle; dominant-baseline: central",
				x="200",
				y="200"
			) { $activeChord }
</template>
