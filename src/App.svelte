<script>
  import { onMount } from "svelte";
  import Box from "./Box.svelte";

  let name = "minesweeper";
  let board = [];
  let boardX = 20;
  let boardY = 20;
  let bombNbr = 40;
  let bombCoor = [];
  // RUN, OVER, WIN
  let gameState = "RUN"
  let isClickInit = false

  function getRandomInt(max) {
    return Math.floor(Math.random() * Math.floor(max));
  }

  function matrix(m, n) {
    return Array.from(
      {
        length: m
      },
      () => Array.from({ length: n }, () => ({ number: 0, visibility: false, clicked: false, marked: false }))
    );
  }

  function increaseNumbersAround(x, y) {
    if (x < boardX && board[x + 1][y].number != -1) {
      board[x + 1][y].number += 1;
    }
    if (x > 0 && board[x - 1][y].number != -1) {
      board[x - 1][y].number += 1;
    }
    if (y < boardY && board[x][y + 1].number != -1) {
      board[x][y + 1].number += 1;
    }
    if (y > 0 && board[x][y - 1].number != -1) {
      board[x][y - 1].number += 1;
    }
    if (x < boardX && y > 0 && board[x + 1][y - 1].number != -1) {
      board[x + 1][y - 1].number += 1;
    }
    if (x < boardX && y < boardY && board[x + 1][y + 1].number != -1) {
      board[x + 1][y + 1].number += 1;
    }
    if (x > 0 && y > 0 && board[x - 1][y - 1].number != -1) {
      board[x - 1][y - 1].number += 1;
    }
    if (x > 0 && y < boardY && board[x - 1][y + 1].number != -1) {
      board[x - 1][y + 1].number += 1;
    }
  }

  function addBomb(clickX, clickY) {
    let tempbomb = bombNbr;
    bombCoor = [];

    while (tempbomb != 0) {
      let x = getRandomInt(boardX - 1);
      let y = getRandomInt(boardY - 1);
      if (board[x][y].number != -1 && (clickX != x || clickY != y)) {
        board[x][y].number = -1;
        bombCoor.push({x: x, y: y});
        increaseNumbersAround(x, y);
        tempbomb--;
      }
    }
  }

  function discoverAround(x, y) {
    if (x < boardX -1 && board[x + 1][y].number != -1 && board[x + 1][y].visibility == false) {
      board[x + 1][y].visibility = true;
      if (board[x + 1][y].number == 0) {
        discoverAround(x + 1, y);
      }
    }
    if (x > 0 && board[x - 1][y].number != -1 && board[x - 1][y].visibility == false) {
      board[x - 1][y].visibility = true;
      if (board[x - 1][y].number == 0) {
        discoverAround(x - 1, y);
      }
    }
    if (y < boardY -1 && board[x][y + 1].number != -1 && board[x][y + 1].visibility == false) {
      board[x][y + 1].visibility = true;
      if (board[x][y + 1].number == 0) {
        discoverAround(x, y + 1);
      }
    }
    if (y > 0 && board[x][y - 1].number != -1 && board[x][y - 1].visibility == false) {
      board[x][y - 1].visibility = true;
      if (board[x][y - 1].number == 0) {
        discoverAround(x, y - 1);
      }
    }
    if (x < boardX - 1 && y > 0 && board[x + 1][y - 1].number != -1 && board[x + 1][y - 1].visibility == false) {
      board[x + 1][y - 1].visibility = true;
      if (board[x + 1][y - 1].number == 0) {
        discoverAround(x + 1, y - 1);
      }
    }
    if (x < boardX - 1 && y < boardY - 1 && board[x + 1][y + 1].number != -1 && board[x + 1][y + 1].visibility == false) {
      board[x + 1][y + 1].visibility = true;
      if (board[x + 1][y + 1].number == 0) {
        discoverAround(x + 1, y + 1);
      }
    }
    if (x > 0 && y > 0 && board[x - 1][y - 1].number != -1 && board[x - 1][y - 1].visibility == false) {
      board[x - 1][y - 1].visibility = true;
      if (board[x - 1][y - 1].number == 0) {
        discoverAround(x - 1, y -1);
      }
    }
    if (x > 0 && y < boardY - 1 && board[x - 1][y + 1].number != -1 && board[x - 1][y + 1].visibility == false) {
      board[x - 1][y + 1].visibility = true;
      if (board[x - 1][y + 1].number == 0) {
        discoverAround(x - 1, y + 1);
      }
    }
  }

  function handleRestart() {
    board = [];
    isClickInit = false
    gameState = 'RUN'
    generateBoard();
  }

  function gameOver() {
    gameState = 'OVER'
    bombCoor.forEach(element => board[element.x][element.y].visibility = true);
  }

  function handleClick(event) {
    if (!isClickInit) {
      addBomb(event.detail.x, event.detail.y);
      isClickInit = true;
    }
    if (!event.detail.type.visibility) {
      board[event.detail.x][event.detail.y].visibility = true;
      
      if (event.detail.type.number == 0) {
        discoverAround(event.detail.x, event.detail.y);
      }
      else if (event.detail.type.number == -1 ) {
        board[event.detail.x][event.detail.y].clicked = true;
        gameOver()
      }
    }
  }

  onMount(async () => {
    generateBoard();
  });

  function generateBoard() {
    board = matrix(boardX, boardY);
    console.log(board);
  }
</script>

<style>
  .minesweeper {
    display: flex;
    flex-direction: column;
    align-items: center;
    font: 20px "Fira Sans", sans-serif;
  }
  p {
    color: purple;
    font-family: "Comic Sans MS", cursive;
    font-size: 2em;
  }
  .row {
    display: flex;
  }
</style>

<div class="minesweeper">
  <p style="color:{ isClickInit ? '#154360' : '#AED6F1' }" >{name}</p>
  <div>
    <input bind:value={name}>
    <button type="button" on:click={handleRestart}>Restart</button>
  </div>
  <div class="board">
    {#each board as row, x}
      <div class="row">
        {#each row as box, y}
          <Box on:click={handleClick} {x} {y} number={box} />
        {/each}
      </div>
    {/each}
  </div>
</div>
