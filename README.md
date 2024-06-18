<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Planning Poker</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .container {
            text-align: center;
            display: none;
        }
        .container.visible {
            display: block;
        }
        .button-container {
            margin-top: 20px;
        }
        .button-container button {
            margin: 10px;
            padding: 10px 20px;
            font-size: 16px;
        }
        .greyed-out {
            background-color: grey;
            cursor: not-allowed;
        }
        .player-box {
            display: inline-block;
            width: 120px;
            height: 120px;
            margin: 10px;
            background-color: #ccc;
            text-align: center;
            vertical-align: top;
            line-height: 1.5;
            border: 1px solid #999;
        }
        .action-buttons {
            margin-top: 20px;
        }
        .card-container {
            margin-top: 20px;
            display: flex;
            justify-content: center;
        }
        .card {
            width: 60px;
            height: 80px;
            margin: 0 5px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 24px;
            border: 1px solid #000;
            cursor: pointer;
            background-color: #fff;
            transition: background-color 0.3s ease;
        }
        .card.selected {
            background-color: green;
            color: white;
        }
        .bottom-right {
            position: absolute;
            bottom: 20px;
            right: 20px;
        }
    </style>
</head>
<body>

<div id="opening-screen" class="container visible">
    <h1>Planning Poker</h1>
    <div class="button-container">
        <button id="join-game">Join Game</button>
    </div>
</div>

<div id="name-screen" class="container">
    <h2>What is your name?</h2>
    <input type="text" id="player-name" placeholder="Enter your name">
    <button id="enter-name" class="greyed-out">Enter</button>
</div>

<div id="game-board" class="container">
    <h1>Planning Poker</h1>
    <div id="players" class="centered"></div>
    <div class="action-buttons">
        <button id="cast-vote" class="greyed-out">Cast your vote</button>
        <button id="reset-votes" class="greyed-out">Reset Votes</button>
        <button id="exit-game">Exit</button>
        <button id="invite-players">Link</button>
    </div>
    <div class="card-container">
        <div class="card">0</div>
        <div class="card">1</div>
        <div class="card">2</div>
        <div class="card">5</div>
        <div class="card">8</div>
        <div class="card">13</div>
        <div class="card">20</div>
        <div class="card">40</div>
        <div class="card">?</div>
    </div>
    <button id="new-game" class="bottom-right">New Game</button>
</div>

<script>
    let playerName = '';
    let players = [];
    let votes = {};
    let votedPlayers = new Set();

    const joinGameButton = document.getElementById('join-game');
    const openingScreen = document.getElementById('opening-screen');
    const nameScreen = document.getElementById('name-screen');
    const gameBoard = document.getElementById('game-board');
    const playerNameInput = document.getElementById('player-name');
    const enterNameButton = document.getElementById('enter-name');
    const playersDiv = document.getElementById('players');
    const castVoteButton = document.getElementById('cast-vote');
    const invitePlayersButton = document.getElementById('invite-players');
    const exitGameButton = document.getElementById('exit-game');
    const resetVotesButton = document.getElementById('reset-votes');
    const newGameButton = document.getElementById('new-game');
    const cards = document.querySelectorAll('.card');

    joinGameButton.addEventListener('click', () => {
        openingScreen.classList.remove('visible');
        nameScreen.classList.add('visible');
    });

    playerNameInput.addEventListener('input', () => {
        if (playerNameInput.value.trim() !== '') {
            enterNameButton.classList.remove('greyed-out');
        } else {
            enterNameButton.classList.add('greyed-out');
        }
    });

    enterNameButton.addEventListener('click', () => {
        if (playerNameInput.value.trim() !== '') {
            playerName = playerNameInput.value.trim();
            players.push(playerName);
            addPlayer(playerName);
            nameScreen.classList.remove('visible');
            gameBoard.classList.add('visible');
        }
    });

    cards.forEach(card => {
        card.addEventListener('click', () => {
            if (!castVoteButton.classList.contains('greyed-out') && playerName !== '') {
                const selectedCard = card.textContent;
                selectCard(card);
                votes[playerName] = selectedCard;
                updateCastVoteButtonState();
            }
        });
    });

    castVoteButton.addEventListener('click', () => {
        if (!castVoteButton.classList.contains('greyed-out') && playerName !== '') {
            castVote(playerName);
            if (votedPlayers.size === players.length) {
                castVoteButton.textContent = 'Show votes!';
                castVoteButton.removeEventListener('click', castVoteHandler);
                castVoteButton.addEventListener('click', showVotes);
                resetVotesButton.classList.remove('greyed-out');
            }
        }
    });

    resetVotesButton.addEventListener('click', () => {
        resetVotes();
    });

    newGameButton.addEventListener('click', () => {
        location.reload();
    });

    invitePlayersButton.addEventListener('click', () => {
        const gameLink = window.location.href;
        alert(`Invite others to join with this link: ${gameLink}`);
    });

    exitGameButton.addEventListener('click', () => {
        location.reload();
    });

    function addPlayer(name) {
        const playerBox = document.createElement('div');
        playerBox.classList.add('player-box');
        playerBox.textContent = name;
        playersDiv.appendChild(playerBox);
    }

    function selectCard(card) {
        cards.forEach(c => c.classList.remove('selected'));
        card.classList.add('selected');
    }

    function updateCastVoteButtonState() {
        const selectedCard = document.querySelector('.card.selected');
        if (selectedCard) {
            castVoteButton.classList.remove('greyed-out');
        } else {
            castVoteButton.classList.add('greyed-out');
        }
    }

    function castVote(player) {
        votedPlayers.add(player);
        const playerBoxes = document.querySelectorAll('.player-box');
        playerBoxes.forEach(box => {
            if (box.textContent === player) {
                box.style.backgroundColor = 'green';
            }
        });
    }

    function showVotes() {
        players.forEach(name => {
            const playerBoxes = document.querySelectorAll('.player-box');
            playerBoxes.forEach(box => {
                if (box.textContent === name) {
                    const voteValue = votes[name] || '?';
                    box.innerHTML = `<div class="card">${voteValue}</div><p>${name}</p>`;
                    box.style.backgroundColor = '#ccc';
                }
            });
        });
        castVoteButton.textContent = 'Cast your vote';
        castVoteButton.classList.add('greyed-out');
        castVoteButton.removeEventListener('click', showVotes);
        castVoteButton.addEventListener('click', castVoteHandler);
        resetVotesButton.classList.add('greyed-out');
    }

    function resetVotes() {
        votes = {};
        votedPlayers.clear();
        cards.forEach(card => card.classList.remove('selected'));
        const playerBoxes = document.querySelectorAll('.player-box');
        playerBoxes.forEach(box => {
            box.style.backgroundColor = '#ccc';
            box.innerHTML = `${box.textContent}`;
        });
        updateCastVoteButtonState();
    }

    function castVoteHandler() {
        castVote(playerName);
    }
</script>

</body>
</html>
