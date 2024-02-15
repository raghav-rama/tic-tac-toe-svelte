<script>
  import Icon from "@iconify/svelte";
  import { tick } from "svelte";
  let cross = "X";
  let zero = "O";
  let space = " ";
  let moves = [
    [space, space, space],
    [space, space, space],
    [space, space, space],
  ];
  let isReset = false;
  let turn = zero;
  let winner;
  $: reset(isReset);
  function moveCursor(e) {
    const darkDiv = document.querySelector("div.dark");
    const darkDivPosition = darkDiv.getBoundingClientRect();
    const darkDivPositionLeft = darkDivPosition.left;
    const darkDivPositionTop = darkDivPosition.top;
    const cursor = document.querySelector(".cursor");
    cursor.style.left = e.clientX - darkDivPositionLeft + "px";
    cursor.style.top = e.clientY - darkDivPositionTop + "px";
  }
  function reset(isReset) {
    if (isReset) {
      for (let i = 0; i < 3; ++i) {
        for (let j = 0; j < 3; ++j) {
          moves[i][j] = space;
        }
      }
    }
  }
  function cellClicked(e, i, j) {
    isReset = false;
    if (moves[i][j] === space) {
      moves[i][j] = turn;
      turn = turn === cross ? zero : cross;
      winner = checkWinner();
      if (winner) {
        isReset = true;
      }
    } else {
      alert("Mark somewhere else!");
    }
  }

  function isSpace(char) {
    return char === space ? undefined : char;
  }

  function checkWinner() {
    for (let i = 0; i < 3; ++i) {
      if (moves[i][0] === moves[i][1] && moves[i][1] === moves[i][2]) {
        return isSpace(moves[i][0]);
      }
      if (moves[0][i] === moves[1][i] && moves[1][i] === moves[2][i]) {
        return isSpace(moves[0][i]);
      }
    }
    if (moves[0][0] === moves[1][1] && moves[1][1] === moves[2][2]) {
      return isSpace(moves[1][1]);
    }
    if (moves[0][2] === moves[1][1] && moves[1][1] === moves[2][0]) {
      return isSpace(moves[1][1]);
    }
    return undefined;
  }
</script>

<div class="dark">
  <h1>{winner ? `${winner} is the Winner!` : ""}</h1>
  <table on:mousemove={moveCursor}>
    {#each moves as move, i}
      <tr>
        {#each move as element, j}
          <td
            on:click={(e) => cellClicked(e, i, j)}
            on:keydown={(e) => {
              if (e.key === "Enter") {
                cellClicked(e, i, j);
              }
            }}
          >
            <span>
              {#if moves[i][j] === cross}
                <Icon icon="radix-icons:cross-1" />
              {:else if moves[i][j] === zero}
                <Icon icon="tabler:letter-o" />
              {/if}
            </span>
          </td>
        {/each}
      </tr>
    {/each}
  </table>
  <span class="cursor">
    {#if turn === cross}
      <Icon icon="radix-icons:cross-1" />
    {:else}
      <Icon icon="tabler:letter-o" />
    {/if}
  </span>
  <button
    on:click={async (e) => {
      await tick();
      isReset = true;
    }}>{"Reset"}</button
  >
</div>

<style>
  .cursor {
    pointer-events: none;
    color: white;
  }
  div {
    width: 30%;
    display: grid;
    place-items: center;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  table {
    cursor: none;
    table-layout: fixed;
    border-collapse: collapse;
    border: 3px solid white;
  }
  span {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  td {
    position: relative;
    padding: 30px;
    border: 1px solid white;
  }
  button {
    margin-top: 5px;
    padding: 5px;
    width: 40%;
  }
</style>
