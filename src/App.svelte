
<script>
	import {onMount} from 'svelte';

	`
1. Array of N size
2. 3 states = { empty = 0 ; snake = int[] -> 1 ; food = 2 }
3. snakePosition = [[x , y]] ( .length += 1 on food )
4. recursive style to do mouvement starting from headofSnake
`

	const n = 24;
	let state = false;
	let score = 0;
	let dir = [0, 0];
	let snake = [[Math.floor(n/2) ,Math.floor(n/2)]];

	function getBoard(n) {
		var snakeBoard = [[]];
		for (var r = 0; r < n; ++r) {
			snakeBoard[r] = [];
			for (var c = 0; c < n; ++c) {
				snakeBoard[r][c] = 0;
			}
		}
		return snakeBoard;
	}

	let snakeBoard = getBoard(n, 0);

	function getRandomInt(max) {   // Function Taken from dev.mozilla.org docs
		return Math.floor(Math.random() * Math.floor(max));
	}
	function randomFood() {
		snakeBoard[getRandomInt(n)][getRandomInt(n)] = 2;
	}
	randomFood();

	function border(x) {
		return x < 0 || x > n - 1;
	}

	function currState(pos , snakeBody , headOfSnake) {
		const [x, y] = pos;
		if (border(headOfSnake[0]) || border(headOfSnake[1])) {
			state = true;
			return false
		}
		if (snakeBody.some((x) => x[0] === headOfSnake[0] && x[1] === headOfSnake[1])) {
			state = true;
			return false
		}
		return true
	}
	$: {
		let c = 0
		for (let i = 0; i < snakeBoard.length; i++) {
			for (let k = 0; k < snakeBoard.length; k++) {
				if (snakeBoard[i][k] === 1) {
					snakeBoard[i][k] = 0;
				}
				if (snakeBoard[i][k] === 2) {
					c += 1;
				}
			}
		}
		if (c == 0) {
			randomFood()
		}
		snake.forEach(([x, y]) => {
			snakeBoard[x][y] = 1;
		});
	}
	function nextGen(n) {
		setTimeout(() => {
			const [x, y] = snake[0];
			const [dx, dy] = dir
			const headOfSnake = [x + dx, y + dy]

			if (border(headOfSnake[0])) {
				state = true;
				return
			}
			let grow = false;
			if (snakeBoard[headOfSnake[0]][headOfSnake[1]] === 2) {
				grow = true;
				score += 1
				randomFood();
			}
			const snakeBody = snake.slice(0, snake.length - (grow ? 0 : 1)
			);

			if (currState([x,y] , snakeBody , headOfSnake)) {
				snake = [headOfSnake, ...snakeBody];
				nextGen(150 - Math.log2(snake.length)*10);
			}
			else state = true;
		}, n)
	}
	onMount(() =>{
		nextGen(150);

	})


	function newGame() {

		 state = false;
		 score = 0;
		 dir = [0, 0];
		 snake = [[Math.floor(n/2) ,Math.floor(n/2)]];
		 snakeBoard = getBoard(n, 0);

		randomFood();
		nextGen(n);

	}


</script>

<svelte:window
		on:keydown={(press) => {
    switch (press.key) {
      case 'ArrowLeft':
        dir = [0, -1];
        break;
      case 'ArrowRight':
        dir = [0, 1];
        break;
      case 'ArrowUp':
        dir = [-1, 0];
        break;
      case 'ArrowDown':
        dir = [1, 0];
        break;
      case 'Enter':
        newGame();
        break;
    }
  }}/>



<main>

	<h1 class="game-container">Shitty Snake</h1>
	<h3 class="game-container">Score: {score}</h3>
	<div class="game-container">
		{#if state === true}
			<h1 class="end">You Lost</h1>
		{/if}
		<div>
			{#each snakeBoard as row, i}
				<div class="row">
					{#each row as cell, j}
						{#if cell === 1}
							<div class="cell snake"></div>
						{:else if cell === 2}
							<div class="cell food"></div>
						{:else}
							<div class="cell empty"></div>
						{/if}
					{/each}
				</div>

			{/each}
		</div>
	</div>

</main>


<style>
	.end {
		position: absolute;
		z-index: 1000;
		top: 25%;
		left: 50%;
		transform: translate(-50% , -25%);
		-webkit-transform: translate(-50%, -25%);
	}

	.cell {
		width: 20px;
		height: 20px;
	}

	.snake {
		background-color: #f1f1f1;
		border-radius: 7px;
	}

	.food {
		background-color: red;
		border-radius: 100px;
		box-shadow: 2px 2px 25px 0px rgba(255,109,109,0.75);
		-webkit-box-shadow: 2px 2px 25px 0px rgba(255,109,109,0.75);
		-moz-box-shadow: 2px 2px 25px 0px rgba(255,109,109,0.75);
		z-index: 50;

	}

	.row {
		display: flex;
		border-radius: 50px;
		box-shadow: 2px 0px 25px 0px rgba(0,0,0,0.55);
		-webkit-box-shadow: 2px 0px 25px 0px rgba(0,0,0,0.55);
		-moz-box-shadow: 2px 0px 25px 0px rgba(0,0,0,0.55);
	}

	.game-container {
		position: relative;
		display: flex;
		justify-content: center;
		align-items: center;

	}

	h1 {
		color: #ffffff;
		font-size: 4em;
		font-weight: 600;
	}

	.empty {
		background-color: #181818;
	}

</style>


