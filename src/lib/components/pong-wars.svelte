<script lang="ts">
	import { onMount } from 'svelte';
	import { Button } from '@/components/ui/button';

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;
	const SQUARE_SIZE = 20;
	const MIN_SPEED = 4;
	const MAX_SPEED = 8;
	let dayScore = 0;
	let nightScore = 0;
	let animationFrameId: number;
	let isPlaying = true; // Control play/pause state
	const colorPalette = {
		ArcticPowder: '#F1F6F4',
		MysticMint: '#D9E8E3',
		WinterGrey: '#b2b2b1',
		Forsythia: '#FFC801',
		DeepSaffron: '#FF9932',
		OceanicNoir: '#172B36',
		Svelte: '#EB4F27',
		Black: '#080808'
	};

	const DAY_COLOR = colorPalette.WinterGrey;
	const DAY_BALL_COLOR = colorPalette.Black;
	const NIGHT_COLOR = colorPalette.Black;
	const NIGHT_BALL_COLOR = colorPalette.WinterGrey;

	let balls: {
		x: number;
		y: number;
		dx: number;
		dy: number;
		reverseColor: string;
		ballColor: string;
	}[] = [];
	let squares: string[][] = [];

	onMount(() => {
		ctx = canvas.getContext('2d') as CanvasRenderingContext2D;
		initGame();
		play();
	});

	function initGame() {
		const numSquaresX = canvas.width / SQUARE_SIZE;
		const numSquaresY = canvas.height / SQUARE_SIZE;

		// Populate the fields, one half day, one half night
		for (let i = 0; i < numSquaresX; i++) {
			squares[i] = [];
			for (let j = 0; j < numSquaresY; j++) {
				squares[i][j] = i < numSquaresX / 2 ? DAY_COLOR : NIGHT_COLOR;
			}
		}

		balls = [
			{
				x: canvas.width / 4,
				y: canvas.height / 2,
				dx: 8,
				dy: -8,
				reverseColor: DAY_COLOR,
				ballColor: DAY_BALL_COLOR
			},
			{
				x: (canvas.width / 4) * 3,
				y: canvas.height / 2,
				dx: -8,
				dy: 8,
				reverseColor: NIGHT_COLOR,
				ballColor: NIGHT_BALL_COLOR
			}
		];
	}

	function draw() {
		ctx.clearRect(0, 0, canvas.width, canvas.height);
		drawSquares();
		balls.forEach((ball) => {
			drawBall(ball);
			checkSquareCollision(ball);
			checkBoundaryCollision(ball);
			addRandomness(ball);
			ball.x += ball.dx;
			ball.y += ball.dy;
		});

		animationFrameId = requestAnimationFrame(draw);
	}

	function drawSquares() {
		dayScore = 0;
		nightScore = 0;

		squares.forEach((row, i) => {
			row.forEach((color, j) => {
				ctx.fillStyle = color;
				ctx.fillRect(i * SQUARE_SIZE, j * SQUARE_SIZE, SQUARE_SIZE, SQUARE_SIZE);

				if (color === DAY_COLOR) dayScore++;
				if (color === NIGHT_COLOR) nightScore++;
			});
		});
	}

	function drawBall(ball: { x: number; y: number; ballColor: string }) {
		ctx.beginPath();
		ctx.arc(ball.x, ball.y, SQUARE_SIZE / 2, 0, Math.PI * 2);
		ctx.fillStyle = ball.ballColor;
		ctx.fill();
		ctx.closePath();
	}

	function checkSquareCollision(ball: {
		x: number;
		y: number;
		dx: number;
		dy: number;
		reverseColor: string;
	}) {
		for (let angle = 0; angle < Math.PI * 2; angle += Math.PI / 4) {
			const checkX = ball.x + Math.cos(angle) * (SQUARE_SIZE / 2);
			const checkY = ball.y + Math.sin(angle) * (SQUARE_SIZE / 2);
			const i = Math.floor(checkX / SQUARE_SIZE);
			const j = Math.floor(checkY / SQUARE_SIZE);

			if (i >= 0 && i < squares.length && j >= 0 && j < squares[i].length) {
				if (squares[i][j] !== ball.reverseColor) {
					squares[i][j] = ball.reverseColor;

					// Introduce randomness to the reflection angle
					const reflectionAngle = (Math.random() * Math.PI) / 4 - Math.PI / 8; // Random angle between -22.5 to 22.5 degrees
					let speed = Math.sqrt(ball.dx * ball.dx + ball.dy * ball.dy);

					// Determine the general direction of reflection
					if (Math.abs(Math.cos(angle)) > Math.abs(Math.sin(angle))) {
						ball.dx = -ball.dx;
					} else {
						ball.dy = -ball.dy;
					}

					// Adjust the ball's direction with the randomized reflection angle
					const newAngle = Math.atan2(ball.dy, ball.dx) + reflectionAngle;
					ball.dx = speed * Math.cos(newAngle);
					ball.dy = speed * Math.sin(newAngle);

					// Ensure the speed stays within predefined limits
					speed = Math.max(MIN_SPEED, Math.min(MAX_SPEED, speed));
					ball.dx = speed * (ball.dx / Math.sqrt(ball.dx * ball.dx + ball.dy * ball.dy));
					ball.dy = speed * (ball.dy / Math.sqrt(ball.dx * ball.dx + ball.dy * ball.dy));
				}
			}
		}
	}

	function checkBoundaryCollision(ball: { x: number; y: number; dx: number; dy: number }) {
		if (ball.x + ball.dx > canvas.width - SQUARE_SIZE / 2 || ball.x + ball.dx < SQUARE_SIZE / 2) {
			ball.dx = -ball.dx;
		}
		if (ball.y + ball.dy > canvas.height - SQUARE_SIZE / 2 || ball.y + ball.dy < SQUARE_SIZE / 2) {
			ball.dy = -ball.dy;
		}
	}

	function addRandomness(ball: { dx: number; dy: number }) {
		ball.dx += Math.random() * 0.01 - 0.005;
		ball.dy += Math.random() * 0.01 - 0.005;
		ball.dx = Math.min(Math.max(ball.dx, -MAX_SPEED), MAX_SPEED);
		ball.dy = Math.min(Math.max(ball.dy, -MAX_SPEED), MAX_SPEED);
		if (Math.abs(ball.dx) < MIN_SPEED) ball.dx = ball.dx > 0 ? MIN_SPEED : -MIN_SPEED;
		if (Math.abs(ball.dy) < MIN_SPEED) ball.dy = ball.dy > 0 ? MIN_SPEED : -MIN_SPEED;
	}

	function play() {
		if (isPlaying) {
			draw();
		}
	}
	function togglePlayPause() {
		isPlaying = !isPlaying;
		if (isPlaying) {
			play();
		} else {
			cancelAnimationFrame(animationFrameId);
		}
	}

	function resetGame() {
		initGame(); // Re-initialize the game state
		if (!isPlaying) {
			togglePlayPause(); // If the game was paused, resume it
		}
	}
</script>

<div class="flex items-center justify-center">
	<div class="flex w-full max-w-4xl flex-col items-center px-4">
		<div class="space-x-6 p-4">
			<Button variant="outline" on:click={togglePlayPause}>{isPlaying ? 'Pause' : 'Play'}</Button>
			<Button variant="outline" on:click={resetGame}>Reset</Button>
		</div>
		<canvas bind:this={canvas} width="600" height="400" class="border-primaray border"></canvas>
		<div class="mt-4 font-mono text-lg">Day {dayScore} | Night {nightScore}</div>
	</div>
</div>

<style>
	canvas {
		display: block;
		margin-top: auto;
		box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
		border-radius: 0.5rem;
		width: 100%; /* Responsive canvas size */
	}
</style>
