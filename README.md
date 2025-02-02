<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minesweeper Game</title>
    <style>
        /* CSS لتنسيق الصفحة */
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }

        .game-container {
            text-align: center;
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(10, 30px);
            grid-template-rows: repeat(10, 30px);
            gap: 2px;
            margin: 20px auto;
        }

        .cell {
            width: 30px;
            height: 30px;
            background-color: #ccc;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            font-size: 14px;
            font-weight: bold;
            border: 1px solid #999;
        }

        .cell.revealed {
            background-color: #fff;
            cursor: default;
        }

        .cell.mine {
            background-color: #ff4444;
            color: white;
        }

        .cell.flag {
            background-color: #ffcc00;
        }

        .status {
            margin-top: 20px;
            font-size: 20px;
            font-weight: bold;
        }

        button {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>Minesweeper</h1>
        <div class="grid"></div>
        <div class="status" id="status">Game in progress...</div>
        <button onclick="resetGame()">Reset Game</button>
    </div>

    <script>
        // إعدادات اللعبة
        const gridSize = 10;
        const mineCount = 15;
        let grid = [];
        let revealedCells = 0;
        let gameOver = false;

        // إنشاء الشبكة
        const gridElement = document.querySelector('.grid');
        const statusElement = document.getElementById('status');

        function createGrid() {
            grid = Array.from({ length: gridSize }, () => Array(gridSize).fill(0));
            revealedCells = 0;
            gameOver = false;
            statusElement.textContent = "Game in progress...";
            gridElement.innerHTML = '';

            // وضع الألغام بشكل عشوائي
            let minesPlaced = 0;
            while (minesPlaced < mineCount) {
                const row = Math.floor(Math.random() * gridSize);
                const col = Math.floor(Math.random() * gridSize);
                if (grid[row][col] !== 'mine') {
                    grid[row][col] = 'mine';
                    minesPlaced++;
                }
            }

            // حساب الأرقام في الخلايا المجاورة
            for (let row = 0; row < gridSize; row++) {
                for (let col = 0; col < gridSize; col++) {
                    if (grid[row][col] === 'mine') continue;
                    let count = 0;
                    for (let i = -1; i <= 1; i++) {
                        for (let j = -1; j <= 1; j++) {
                            if (i === 0 && j === 0) continue;
                            const newRow = row + i;
                            const newCol = col + j;
                            if (newRow >= 0 && newRow < gridSize && newCol >= 0 && newCol < gridSize && grid[newRow][newCol] === 'mine') {
                                count++;
                            }
                        }
                    }
                    grid[row][col] = count;
                }
            }

            // إنشاء الخلايا في الواجهة
            for (let row = 0; row < gridSize; row++) {
                for (let col = 0; col < gridSize; col++) {
                    const cell = document.createElement('div');
                    cell.classList.add('cell');
                    cell.dataset.row = row;
                    cell.dataset.col = col;
                    cell.addEventListener('click', () => revealCell(row, col));
                    cell.addEventListener('contextmenu', (e) => {
                        e.preventDefault();
                        toggleFlag(row, col);
                    });
                    gridElement.appendChild(cell);
                }
            }
        }

        // كشف الخلية
        function revealCell(row, col) {
            if (gameOver) return;
            const cell = document.querySelector(`.cell[data-row="${row}"][data-col="${col}"]`);
            if (cell.classList.contains('revealed') || cell.classList.contains('flag')) return;

            cell.classList.add('revealed');
            revealedCells++;

            if (grid[row][col] === 'mine') {
                cell.textContent = '💣';
                cell.classList.add('mine');
                gameOver = true;
                statusElement.textContent = "Game Over! You hit a mine.";
                revealAllMines();
            } else if (grid[row][col] === 0) {
                cell.textContent = '';
                for (let i = -1; i <= 1; i++) {
                    for (let j = -1; j <= 1; j++) {
                        if (i === 0 && j === 0) continue;
                        const newRow = row + i;
                        const newCol = col + j;
                        if (newRow >= 0 && newRow < gridSize && newCol >= 0 && newCol < gridSize) {
                            revealCell(newRow, newCol);
                        }
                    }
                }
            } else {
                cell.textContent = grid[row][col];
            }

            if (revealedCells === gridSize * gridSize - mineCount) {
                gameOver = true;
                statusElement.textContent = "Congratulations! You won!";
            }
        }

        // وضع أو إزالة العلم
        function toggleFlag(row, col) {
            if (gameOver) return;
            const cell = document.querySelector(`.cell[data-row="${row}"][data-col="${col}"]`);
            if (cell.classList.contains('revealed')) return;
            cell.classList.toggle('flag');
        }

        // كشف جميع الألغام عند الخسارة
        function revealAllMines() {
    for (let row = 0; row < gridSize; row++) {
        for (let col = 0; col < gridSize; col++) {
            if (grid[row][col] === 'mine') {
                const cell = document.querySelector(`.cell[data-row="${row}"][data-col="${col}"]`);
                cell.textContent = '💣';
                cell.classList.add('mine');
            }
        }
    }
}


        // إعادة تعيين اللعبة
        function resetGame() {
            createGrid();
        }

        // بدء اللعبة
        createGrid();
    </script>
</body>
</html>
