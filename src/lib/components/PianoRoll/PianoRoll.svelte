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

	const pianoKeyWidth = 64;

	const midiBlockWidth = 48;
	const midiBlockHeight = 18;

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

	function createPIXIApp(canvas: HTMLCanvasElement) {
		PIXI.settings.SORTABLE_CHILDREN = true;

		const app = new PIXI.Application({
			view: canvas,
			height: Math.floor(canvas.clientHeight / 16) * 16,
			width: Math.floor(canvas.clientWidth / 48) * 48,
			backgroundColor: 0x000000
		});

		return app;
	}

	function drawPianoKeys(app: PIXI.Application) {
		for (let octaveNumber = 0; octaveNumber < 8; octaveNumber++) {
			for (let keyNumber = 0; keyNumber < 12; keyNumber++) {
				const pianoKey = new PIXI.Sprite(PIXI.Texture.WHITE);
				const keyColor = octave[keyNumber].color === 'white' ? 0xffffff : 0x000000;
				pianoKey.tint = keyColor;
				pianoKey.height = midiBlockHeight;
				pianoKey.width = pianoKeyWidth;
				pianoKey.position.set(0, keyNumber * midiBlockHeight + octaveNumber * 12 * midiBlockHeight);
				app.stage.addChild(pianoKey);
			}
		}
	}

	function drawPianoGrid(app: PIXI.Application) {
		for (let octaveNumber = 0; octaveNumber < 7; octaveNumber++) {
			drawPianoGridOctave(app, octaveNumber);
		}

		drawGridLines(app);
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
				line.zIndex = 5;

				pianoGrid.stage.addChild(line);
			}

			if (i == octave.length) {
				const line = new PIXI.Graphics();

				line.lineStyle(2, thinLineColor, 1);

				line.moveTo(0, (i + octave.length * octaveNumber) * midiBlockHeight);
				line.lineTo(pianoGrid.screen.width, (i + octave.length * octaveNumber) * midiBlockHeight);
				line.zIndex = 5;

				pianoGrid.stage.addChild(line);
			}
		}
	}

	function drawPianoGridRow(pianoGrid: PIXI.Application, rowNumber: number, color: string) {
		for (let i = 0; i < pianoGrid.screen.width / midiBlockWidth; i++) {
			const midiBlock = new PIXI.Sprite(PIXI.Texture.WHITE);
			midiBlock.position.set(pianoKeyWidth + i * midiBlockWidth, rowNumber * midiBlockHeight);
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

			line.moveTo(i * midiBlockWidth + pianoKeyWidth, 0);
			line.lineTo(i * midiBlockWidth + pianoKeyWidth, pianoGrid.screen.height);

			pianoGrid.stage.addChild(line);
		}
	}

	onMount(() => {
		const canvas = document.getElementById('pianoroll') as HTMLCanvasElement;
		resizeCanvasToDisplaySize(canvas);
		const app = createPIXIApp(canvas);
		drawPianoGrid(app);
		drawPianoKeys(app);
	});
</script>

<canvas id="pianoroll" />

<style>
	#pianoroll {
		width: 100%;
		height: 100%;
	}
</style>
