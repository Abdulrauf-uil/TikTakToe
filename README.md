# TikTakToe
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Tic Tac Toe with AI</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      background: #f2f2f2;
    }

    h1 {
      margin-top: 30px;
    }

    #game {
      display: grid;
      grid-template-columns: repeat(3, 100px);
      grid-gap: 10px;
      justify-content: center;
      margin: 30px auto;
    }

    .cell {
      width: 100px;
      height: 100px;
      font-size: 2.5rem;
      font-weight: bold;
      cursor: pointer;
      background: white;
      border: 2px solid #333;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: background 0.3s;
    }

    .cell:hover {
      background: #eee;
    }

    #status {
      font-size: 1.5rem;
      margin: 20px;
    }

    #reset {
      padding: 10px 20px;
      font-size: 1rem;
      background: #333;
      color: white;
      border: none;
      cursor: pointer;
      border-radius: 5px;
    }

    #reset:hover {
      background: #555;
    }
  </style>
</head>
<body>
  <h1>Tic Tac Toe (You vs AI)</h1>
  <div id="game"></div>
  <div id="status"></div>
  <button id="reset">Reset Game</button>

  <script>
    const gameBoard = document.getElementById("game");
    const statusDisplay = document.getElementById("status");
    const resetButton = document.getElementById("reset");

    let board = ["", "", "", "", "", "", "", "", ""];
    let gameActive = true;

    const human = "X";
    const ai = "O";

    const winningConditions = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6]
    ];

    function handleCellClick(e) {
      const index = e.target.dataset.index;
      if (board[index] !== "" || !gameActive) return;

      makeMove(index, human);

      if (checkWin(human)) {
        statusDisplay.textContent = "You win! 🎉";
        gameActive = false;
        return;
      }

      if (isDraw()) {
        statusDisplay.textContent = "It's a draw! 🤝";
        gameActive = false;
        return;
      }

      statusDisplay.textContent = "AI is thinking...";

      setTimeout(() => {
        const aiIndex = getBestMove();
        makeMove(aiIndex, ai);

        if (checkWin(ai)) {
          statusDisplay.textContent = "AI wins! 💻";
          gameActive = false;
        } else if (isDraw()) {
          statusDisplay.textContent = "It's a draw! 🤝";
          gameActive = false;
        } else {
          statusDisplay.textContent = "Your turn (X)";
        }
      }, 500); // short delay for realism
    }

    function makeMove(index, player) {
      board[index] = player;
      const cell = document.querySelector(`[data-index='${index}']`);
      if (cell) cell.textContent = player;
    }

    function getBestMove() {
      // 1. Win if possible
      for (let i = 0; i < 9; i++) {
        if (board[i] === "") {
          board[i] = ai;
          if (checkWin(ai)) {
            board[i] = "";
            return i;
          }
          board[i] = "";
        }
      }

      // 2. Block human win
      for (let i = 0; i < 9; i++) {
        if (board[i] === "") {
          board[i] = human;
          if (checkWin(human)) {
            board[i] = "";
            return i;
          }
          board[i] = "";
        }
      }

      // 3. Pick center if free
      if (board[4] === "") return 4;

      // 4. Pick a random available cell
      const available = board.map((val, i) => val === "" ? i : null).filter(i => i !== null);
      return available[Math.floor(Math.random() * available.length)];
    }

    function checkWin(player) {
      return winningConditions.some(condition => {
        return condition.every(index => board[index] === player);
      });
    }

    function isDraw() {
      return board.every(cell => cell !== "");
    }

    function createBoard() {
      gameBoard.innerHTML = "";
      board = ["", "", "", "", "", "", "", "", ""];
      gameActive = true;
      statusDisplay.textContent = "Your turn (X)";

      for (let i = 0; i < 9; i++) {
        const cell = document.createElement("div");
        cell.classList.add("cell");
        cell.dataset.index = i;
        cell.addEventListener("click", handleCellClick);
        gameBoard.appendChild(cell);
      }
    }

    resetButton.addEventListener("click", createBoard);

    // Initialize game
    createBoard();
  </script>
</body>
</html>
