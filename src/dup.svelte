
<script>
    import {onMount} from 'svelte';

    `
1. Array of N size
2. 3 states = { empty = 0 ; snake = int[] -> 1 ; food = 2 }
3. snakePosition = [[x , y]] ( .length += 1 on food )
4. recursive style to do mouvement starting from headofSnake
`

    const n = 20;
    let state = false;
    let score = 0;
    let dir = [0, 1];
    let snake = [[Math.floor(n/2) ,Math.floor(n/2)]];
    let headOfSnake = snake[0]

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
        const [x ,y] = pos;
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
        for (let i = 0; i < snakeBoard.length; i++) {
            for (let k = 0; k < snakeBoard.length; k++) {
                if (snakeBoard[i][k] === 1) {
                    snakeBoard[i][k] = 0;
                }
            }
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

            let grow = false;
            if (snakeBoard[headOfSnake[0]][headOfSnake[1]] === 2) {
                grow = true;
                randomFood();
            }
            const snakeBody = snake.slice(0, snake.length - (grow ? 0 : 1)
            );

            if (currState([x,y] , snakeBody , headOfSnake)) {
                snake = [headOfSnake, ...snakeBody];
                nextGen(150);
            }
        }, 150)
    }
    onMount(() =>{
        nextGen(150);

    })




    function restart() {

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
        restart();
        break;
    }
  }}/>



<main>
    <h1>Insert Snake Here</h1>
    <div class="game-container">
        <div>
            {#each snakeBoard as row, i}
                <div class="row">
                    {#each row as cell, j}
                        {#if cell === 1}
                            <div class="square snake"></div>
                        {:else if cell === 2}
                            <div class="square food"></div>
                        {:else}
                            <div class="square empty"></div>
                        {/if}
                    {/each}
                </div>

            {/each}
        </div>
    </div>

</main>


<style>

    .square {
        width: 20px;
        height: 20px;
        border: solid 1px #453434;
    }

    .snake {
        background-color: #53e5ff;
    }

    .food {
        background-color: chartreuse;
    }

    .row {
        display: flex;
    }

    .game-container {
        display: flex;
        justify-content: center;
        align-items: center;
    }

    h1 {
        color: #53e5ff;
        text-transform: uppercase;
        font-size: 4em;
        font-weight: 100;
    }

    .empty {
        background-color: #ff1c1c;
    }

</style>

