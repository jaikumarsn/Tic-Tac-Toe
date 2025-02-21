const board = ["", "", "", "", "", "", "", "", ""];
let currentPlayer = "X";
const winningCombos = [
    [0, 1, 2], [3, 4, 5], [6, 7, 8],
    [0, 3, 6], [1, 4, 7], [2, 5, 8],
    [0, 4, 8], [2, 4, 6]
];

function makeMove(index) {
    if (board[index] === "" && currentPlayer === "X") {
        board[index] = "X";
        if (!checkWinner()) {
            setTimeout(aiMove, 500);
        }
    }
    renderBoard();
}

function aiMove() {
    let bestScore = -Infinity;
    let move;
    for (let i = 0; i < board.length; i++) {
        if (board[i] === "") {
            board[i] = "O";
            let score = minimax(board, 0, false);
            board[i] = "";
            if (score > bestScore) {
                bestScore = score;
                move = i;
            }
        }
    }
    board[move] = "O";
    checkWinner();
    renderBoard();
}

function minimax(board, depth, isMaximizing) {
    let result = checkWinner(true);
    if (result === "X") return -10;
    if (result === "O") return 10;
    if (!board.includes("")) return 0;
    
    if (isMaximizing) {
        let bestScore = -Infinity;
        for (let i = 0; i < board.length; i++) {
            if (board[i] === "") {
                board[i] = "O";
                let score = minimax(board, depth + 1, false);
                board[i] = "";
                bestScore = Math.max(score, bestScore);
            }
        }
        return bestScore;
    } else {
        let bestScore = Infinity;
        for (let i = 0; i < board.length; i++) {
            if (board[i] === "") {
                board[i] = "X";
                let score = minimax(board, depth + 1, true);
                board[i] = "";
                bestScore = Math.min(score, bestScore);
            }
        }
        return bestScore;
    }
}

function checkWinner(simulate = false) {
    for (const combo of winningCombos) {
        const [a, b, c] = combo;
        if (board[a] && board[a] === board[b] && board[a] === board[c]) {
            if (!simulate) alert(`${board[a]} wins!`);
            return board[a];
        }
    }
    if (!board.includes("")) {
        if (!simulate) alert("It's a draw!");
        return "Draw";
    }
    return null;
}

function renderBoard() {
    document.getElementById("game").innerHTML = board.map((cell, index) => 
        `<div class='cell' onclick='makeMove(${index})'>${cell}</div>`
    ).join("");
}

renderBoard();
