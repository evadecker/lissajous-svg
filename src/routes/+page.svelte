<script lang="ts">
	import { onMount } from 'svelte';
	import { Pane } from 'tweakpane';

	let params = {
		angularFrequency: { x: 3, y: 4 },
		phaseShift: Math.PI / 2,
		strokeWidth: 1,
		gridCount: 4,
		gridTickCount: 4,
		gridTickSize: 2,
		gridStrokeWidth: 0.5,
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
			if (count === 0) return;

      const group = document.createElementNS('http://www.w3.org/2000/svg', 'g');
      group.classList.add('grid');
      group.setAttribute('stroke', `var(--${params.color}-4)`);
				group.setAttribute('stroke-width', `${width}`);

			// Major horizontal lines
			for (let i = 0; i <= count; i++) {
				const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
				line.setAttribute('x1', '0');
				line.setAttribute('y1', `${((200 - width) / count) * i + width / 2}`);
				line.setAttribute('x2', '200');
				line.setAttribute('y2', `${((200 - width) / count) * i + width / 2}`);
				group.appendChild(line);
			}

			// Major vertical lines
			for (let i = 0; i <= count; i++) {
				const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
				line.setAttribute('x1', `${((200 - width) / count) * i + width / 2}`);
				line.setAttribute('y1', `${width}`);
				line.setAttribute('x2', `${((200 - width) / count) * i + width / 2}`);
				line.setAttribute('y2', `${200 - width}`);
				group.appendChild(line);
			}

			// Minor horizontal tickmarks
			for (let i = 0; i <= count * (params.gridTickCount + 1); i++) {
				const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
				line.setAttribute('x1', `${200 / 2 - params.gridTickSize}`);
				line.setAttribute(
					'y1',
					`${((200 - width) / count / (params.gridTickCount + 1)) * i + width / 2}`
				);
				line.setAttribute('x2', `${200 / 2 + params.gridTickSize}`);
				line.setAttribute(
					'y2',
					`${((200 - width) / count / (params.gridTickCount + 1)) * i + width / 2}`
				);
				group.appendChild(line);
			}

			// Minor vertical tickmarks
			for (let i = 0; i <= count * (params.gridTickCount + 1); i++) {
				const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
				line.setAttribute(
					'x1',
					`${((200 - width) / count / (params.gridTickCount + 1)) * i + width / 2}`
				);
				line.setAttribute('y1', `${200 / 2 - params.gridTickSize}`);
				line.setAttribute(
					'x2',
					`${((200 - width) / count / (params.gridTickCount + 1)) * i + width / 2}`
				);
				line.setAttribute('y2', `${200 / 2 + params.gridTickSize}`);
				group.appendChild(line);
			}

      svg.appendChild(group);
		};

		const drawLissajous = (svg: Element, a: number, b: number) => {
      const group = document.createElementNS('http://www.w3.org/2000/svg', 'g');
      group.classList.add('lissajous');
      group.setAttribute('fill', 'none');
			group.setAttribute('stroke', `var(--${params.color}-9)`);
			group.setAttribute('stroke-width', `${params.strokeWidth}`);
			group.setAttribute('stroke-linecap', 'square');
			group.setAttribute('stroke-linejoin', 'round');

			const path = document.createElementNS('http://www.w3.org/2000/svg', 'path');
			const points = [];

			for (let t = 0; t <= Math.PI * 2 + 0.1; t += 0.01) {
				const x = Math.sin(a * t + params.phaseShift);
				const y = Math.sin(b * t);
				points.push(`${x * 80 + 100},${y * 80 + 100}`);
			}

			path.setAttribute('d', `M${points.join('L')}`);
			group.appendChild(path);

      svg.appendChild(group);
		};

		if (svg) {
			drawGrid(svg, params.gridCount, params.gridStrokeWidth);
			drawLissajous(svg, params.angularFrequency.x, params.angularFrequency.y);
		}
	};

	const setupPane = () => {
		const pane = new Pane({
			container: document.getElementById('controls')!,
		});

		const equation = pane.addFolder({
			title: 'Lissajous Curve'
		});

		equation.addBinding(params, 'angularFrequency', {
			label: 'frequency',
			picker: 'inline',
			expanded: true,
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

		equation.addBinding(params, 'phaseShift', {
			label: 'phase',
			min: 0,
			max: Math.PI,
			step: 0.01,
		});

		equation.addBinding(params, 'strokeWidth', {
			label: 'stroke',
			min: 0.5,
			max: 3,
			step: 0.1
		});

		const grid = pane.addFolder({
			title: 'Grid'
		});

		grid.addBinding(params, 'gridCount', {
			label: 'gridlines',
			min: 0,
			max: 12,
			step: 2
		});

		grid.addBinding(params, 'gridTickCount', {
			label: 'tickmarks',
			min: 0,
			max: 8,
			step: 1
		});

		grid.addBinding(params, 'gridTickSize', {
			label: 'tick size',
			min: 1,
			max: 3,
			step: 0.1
		});

		grid.addBinding(params, 'gridStrokeWidth', {
			label: 'stroke',
			min: 0.2,
			max: 1,
			step: 0.1
		});

		const style = pane.addFolder({
			title: 'Style'
		});

		style.addBinding(params, 'color', {
			options: {
				red: 'red',
				orange: 'orange',
				yellow: 'amber',
        lime: 'lime',
				teal: 'teal',
				violet: 'violet',
        pink: 'pink'
			}
		});

    const download = pane.addFolder({
      title: 'Download'
    });

    const btn = download.addButton({
      title: 'Download SVG',
    });

    btn.on('click', () => {
      const svg = document.querySelector('.canvas');

      if (!svg) return;
      svg.setAttribute('stroke', '#000');

      const svgData = new XMLSerializer().serializeToString(svg);
      const blob = new Blob([svgData], { type: 'image/svg+xml' });
      const url = URL.createObjectURL(blob);

      const a = document.createElement('a');
      a.href = url;
      a.download = 'lissajous.svg';
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);
    });

		pane.on('change', () => {
			draw();
		});
	};

	onMount(() => {
		setupPane();
		draw();
	});
</script>

<div class="content">
	<h1 class="visually-hidden">Lissajous Curve Generator</h1>
	<svg class="canvas" viewBox="0 0 200 200"></svg>
	<div class="lcd"></div>
	<div id="controls"></div>
</div>

<style>
  @keyframes pulse {
    0% {
      opacity: 0.02;
    }
    50% {
      opacity: 0.026;
    }
    100% {
      opacity: 0.02;
    }
  }

	.content {
		padding: 24px;
		display: flex;
		gap: 24px;
	}

	@media (width < 760px) {
		.content {
			flex-direction: column;
			align-items: center;
		}
	}

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
		max-width: 100%;
		flex: 1;
	}

  .lcd {
    position: fixed;
    inset: 0;
    width: 100%;
    height: 100%;
    background: repeating-linear-gradient(#fff, #fff 2px, #000 2px, #000 4px);
    animation: pulse 0.1s infinite;
  }

	#controls {
		flex-shrink: 0;
	}
</style>
