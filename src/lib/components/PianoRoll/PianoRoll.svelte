<script lang="ts">
	import { onMount } from 'svelte';
	import * as PIXI from 'pixi.js';

	const octave = [
		{ note: 'c', color: 'white' },
		{ note: 'cSharp', color: 'black' },
		{ note: 'd', color: 'white' },
		{ note: 'dSharp', color: 'black' },
		{ note: 'e', color: 'white' },
		{ note: 'f', color: 'white' },
		{ note: 'fSharp', color: 'black' },
		{ note: 'g', color: 'white' },
		{ note: 'gSharp', color: 'black' },
		{ note: 'a', color: 'white' },
		{ note: 'aSharp', color: 'black' },
		{ note: 'b', color: 'white' }
	];

	const midiBlockWidth = 48;
	const midiBlockHeight = 16;

	const thinLineColor = 0x343645;
	const thickLineColor = 0x3f4453;

	const whiteKeyMidiColor = 0x272935;
	const blackKeyMidiBlockColor = 0x1e2029;

	function resizeCanvasToDisplaySize(canvas: HTMLCanvasElement) {
		// look up the size the canvas is being displayed
		var width = canvas.clientWidth;
		var height = canvas.clientHeight;

		// If it's resolution does not match change it
		if (canvas.width !== width || canvas.height !== height) {
			canvas.width = width;
			canvas.height = height;
			return true;
		}

		return false;
	}

	function drawPianoGrid(canvas: HTMLCanvasElement) {
		const pianoGrid = new PIXI.Application({
			view: canvas,
			height: Math.floor(canvas.clientHeight / 16) * 16,
			width: Math.floor(canvas.clientWidth / 48) * 48,
			backgroundColor: 0x000000
		});

		for (let octaveNumber = 0; octaveNumber < 7; octaveNumber++) {
			drawPianoGridOctave(pianoGrid, octaveNumber);
		}

		drawGridLines(pianoGrid);
	}

	function drawPianoGridOctave(pianoGrid: PIXI.Application, octaveNumber: number) {
		for (let i = 0; i < octave.length; i++) {
			drawPianoGridRow(pianoGrid, i + octaveNumber * 12, octave[i].color);
		}

		for (let i = 0; i <= octave.length; i++) {
			if (i == 5) {
				const line = new PIXI.Graphics();

				line.lineStyle(2, thinLineColor, 1);

				line.moveTo(0, (i + octave.length * octaveNumber) * midiBlockHeight);
				line.lineTo(pianoGrid.screen.width, (i + octave.length * octaveNumber) * midiBlockHeight);

				pianoGrid.stage.addChild(line);
			}

			if (i == octave.length) {
				const line = new PIXI.Graphics();

				line.lineStyle(2, thinLineColor, 1);

				line.moveTo(0, (i + octave.length * octaveNumber) * midiBlockHeight);
				line.lineTo(pianoGrid.screen.width, (i + octave.length * octaveNumber) * midiBlockHeight);

				pianoGrid.stage.addChild(line);
			}
		}
	}

	function drawPianoGridRow(pianoGrid: PIXI.Application, rowNumber: number, color: string) {
		for (let i = 0; i < pianoGrid.screen.width / midiBlockWidth; i++) {
			const midiBlock = new PIXI.Sprite(PIXI.Texture.WHITE);
			midiBlock.position.set(i * midiBlockWidth, rowNumber * midiBlockHeight);
			midiBlock.width = midiBlockWidth;
			midiBlock.height = midiBlockHeight;

			let midiBlockColor = color === 'white' ? whiteKeyMidiColor : blackKeyMidiBlockColor;

			midiBlock.tint = midiBlockColor;
			pianoGrid.stage.addChild(midiBlock);
		}
	}

	function drawGridLines(pianoGrid: PIXI.Application) {
		for (let i = 3; i < pianoGrid.screen.width / midiBlockWidth; i = i + 4) {
			const line = new PIXI.Graphics();
			let lineColor = i % 4 == 0 ? thinLineColor : thickLineColor;

			line.lineStyle(2, lineColor, 1);

			line.moveTo(i * midiBlockWidth, 0);
			line.lineTo(i * midiBlockWidth, pianoGrid.screen.height);

			pianoGrid.stage.addChild(line);
		}
	}

	onMount(() => {
		const canvas = document.getElementById('pianoroll') as HTMLCanvasElement;
		resizeCanvasToDisplaySize(canvas);
		drawPianoGrid(canvas);
	});
</script>

<canvas id="pianoroll" />

<style>
	#pianoroll {
		width: 100%;
		height: 100%;
	}
</style>
