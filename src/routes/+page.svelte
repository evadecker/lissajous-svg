<script lang="ts">
	import { onMount } from 'svelte';
	import { Pane } from 'tweakpane';
	import { browser } from '$app/environment';

	if (browser) {
		let params = {
			angularFrequency: { x: 3, y: 4 },
			gridCount: 20,
			color: 'orange'
		};

		const draw = () => {
			const svg = document.querySelector('.canvas');

			// Clear contents
			if (svg) {
				while (svg.firstChild) {
					svg.removeChild(svg.firstChild);
				}
			}

			const drawGrid = (svg: Element, count: number, width: number) => {
				for (let i = 0; i <= count; i++) {
					const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
					line.setAttribute('x1', '0');
					line.setAttribute('y1', `${((200 - width) / count) * i + width / 2}`);
					line.setAttribute('x2', '200');
					line.setAttribute('y2', `${((200 - width) / count) * i + width / 2}`);
					line.setAttribute('stroke', `var(--${params.color}-3)`);
					line.setAttribute('stroke-width', `${width}`);
					svg.appendChild(line);
				}

				for (let i = 0; i <= count; i++) {
					const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
					line.setAttribute('x1', `${((200 - width) / count) * i + width / 2}`);
					line.setAttribute('y1', `${width}`);
					line.setAttribute('x2', `${((200 - width) / count) * i + width / 2}`);
					line.setAttribute('y2', `${200 - width}`);
					line.setAttribute('stroke', `var(--${params.color}-3)`);
					line.setAttribute('stroke-width', '0.5');
					svg.appendChild(line);
				}
			};

			const drawLissajous = (svg: Element, a: number, b: number) => {
				const path = document.createElementNS('http://www.w3.org/2000/svg', 'path');
				const points = [];

				for (let t = 0; t <= Math.PI * 2 + 0.1; t += 0.01) {
					const x = Math.sin(a * t);
					const y = Math.sin(b * t);
					points.push(`${x * 80 + 100},${y * 80 + 100}`);
				}

				path.setAttribute('d', `M${points.join('L')}`);
				path.setAttribute('fill', 'none');
				path.setAttribute('stroke', `var(--${params.color}-9)`);
				path.setAttribute('stroke-width', '1');
				path.setAttribute('stroke-linecap', 'square');
				path.setAttribute('stroke-linejoin', 'round');
				svg.appendChild(path);
			};

			if (svg) {
				drawGrid(svg, params.gridCount, 0.5);
				drawLissajous(svg, params.angularFrequency.x, params.angularFrequency.y);
			}
		};

		onMount(() => {
			const pane = new Pane();

			pane.addBinding(params, 'angularFrequency', {
				label: 'frequency',
				x: {
					min: -16,
					max: 16,
					step: 1
				},
				y: {
					min: -16,
					max: 16,
					step: 1
				}
			});

			pane.addBinding(params, 'gridCount', {
				label: 'grid count',
				min: 1,
				max: 50,
				step: 1
			});

			pane.addBinding(params, 'color', {
				options: {
					red: 'red',
					orange: 'orange',
					yellow: 'amber',
					teal: 'teal',
					violet: 'violet'
				}
			});

			pane.on('change', () => {
				draw();
			});

			draw();
		});
	}
</script>

<svelte:head>
	<title>Lissajous Curve Generator</title>
</svelte:head>

<h1 class="visually-hidden">Lissajous Curve Generator</h1>
<svg class="canvas" viewBox="0 0 200 200"></svg>

<style>
	.visually-hidden {
		clip: rect(0 0 0 0);
		clip-path: inset(50%);
		height: 1px;
		overflow: hidden;
		position: absolute;
		white-space: nowrap;
		width: 1px;
	}

	.canvas {
		width: 90vmin;
		height: 90vmin;
	}
</style>
