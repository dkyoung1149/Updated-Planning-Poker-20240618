<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Planning Poker</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 20px;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
        }
        .button {
            display: inline-block;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            background-color: #007bff;
            color: #fff;
            border: none;
            border-radius: 5px;
            margin-top: 10px;
        }
        .button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        .input-field {
            padding: 10px;
            width: 100%;
            margin-bottom: 10px;
            box-sizing: border-box;
        }
        .player-box {
            display: inline-block;
            width: 100px;
            height: 80px;
            background-color: #f0f0f0;
            border: 1px solid #ccc;
            margin: 10px;
            padding: 5px;
            text-align: center;
            vertical-align: top;
        }
        .vote-card {
            display: inline-block;
            width: 60px;
            height: 90px;
            background-color: #fff;
            border: 1px solid #000;
            margin: 5px;
            padding: 5px;
            text-align: center;
            cursor: pointer;
        }
        .selected {
            background-color: lightgreen;
        }
    </style>
</head>
<body>
    <div class="container" id="opening-screen">
        <h1>Planning Poker</h1>
        <button class="button" onclick="showNameInput()">Join Game</button>
    </div>

    <div class="container" id="name-input-screen" style="display: none;">
        <h2>What is your name?</h2>
        <input type="text" id="playerName" class="input-field" placeholder="Enter your name">
        <button class="button" id="enterButton" onclick="enterName()" disabled>Enter</button>
    </div>

    <div class="container" id="game-board" style="display: none;">
        <h2>Planning Poker</h2>
        
        <div id="playerBoxes">
            <!-- Player boxes will be dynamically added here -->
        </div>

        <div>
            <button class="button" id="castVoteButton" onclick="castVote()" disabled>Cast your vote</button>
            <button class="button" id="resetVotesButton" onclick="resetVotes()" disabled>Reset Votes</button>
            <button class="button" onclick="exitGame()">Exit</button>
        </div>

        <div id="voteCards">
            <!-- Voting cards will be dynamically added here -->
        </div>

        <button class="button" id="showVotesButton" onclick="showVotes()" style="display: none;">Show votes!</button>
    </div>

    <script>
        let players = [];
        let selectedCard = null;
        let votesSubmitted = false;

        function showNameInput() {
            document.getElementById('opening-screen').style.display = 'none';
            document.getElementById('name-input-screen').style.display = 'block';
        }

        function enterName() {
            const playerName = document.getElementById('playerName').value.trim();
            if (playerName !== '') {
                players.push(playerName);
                document.getElementById('name-input-screen').style.display = 'none';
                setupGameBoard();
            }
        }

        function setupGameBoard() {
            document.getElementById('game-board').style.display = 'block';

            // Display player boxes
            let playerBoxesHtml = '';
            players.forEach(player => {
                playerBoxesHtml += `<div class="player-box" id="${player}-box">${player}</div>`;
            });
            document.getElementById('playerBoxes').innerHTML = playerBoxesHtml;

            // Display voting cards
            const votingCards = [0, 1, 2, 5, 8, 13, 20, 40, '?'];
            let voteCardsHtml = '';
            votingCards.forEach(card => {
                voteCardsHtml += `<div class="vote-card" id="card-${card}" onclick="selectCard('${card}')">${card}</div>`;
            });
            document.getElementById('voteCards').innerHTML = voteCardsHtml;
        }

        function selectCard(card) {
            if (!votesSubmitted) {
                selectedCard = card;
                const cards = document.querySelectorAll('.vote-card');
                cards.forEach(cardElement => {
                    cardElement.classList.remove('selected');
                });
                document.getElementById(`card-${card}`).classList.add('selected');
                document.getElementById('castVoteButton').disabled = false;
            }
        }

        function castVote() {
            if (!votesSubmitted) {
                // Simulating vote casting (change color of player box)
                players.forEach(player => {
                    const playerBox = document.getElementById(`${player}-box`);
                    playerBox.style.backgroundColor = 'lightgreen';
                });
                document.getElementById('castVoteButton').disabled = true;
                document.getElementById('resetVotesButton').disabled = false;
                document.getElementById('showVotesButton').style.display = 'inline-block';
                votesSubmitted = true;
            }
        }

        function resetVotes() {
            if (votesSubmitted) {
                // Reset vote display (change color of player box back to default)
                players.forEach(player => {
                    const playerBox = document.getElementById(`${player}-box`);
                    playerBox.style.backgroundColor = '#f0f0f0';
                    playerBox.innerHTML = player;
                });
                document.getElementById('castVoteButton').disabled = false;
                document.getElementById('resetVotesButton').disabled = true;
                document.getElementById('showVotesButton').style.display = 'none';
                selectedCard = null;
                document.querySelectorAll('.vote-card').forEach(card => {
                    card.classList.remove('selected');
                });
                votesSubmitted = false;
            }
        }

        function showVotes() {
            if (votesSubmitted) {
                // Display selected votes in player boxes
                players.forEach(player => {
                    const playerBox = document.getElementById(`${player}-box`);
                    playerBox.innerHTML = `<div>${selectedCard}</div><div>${player}</div>`;
                });
            }
        }

        function exitGame() {
            // Reset game state and return to opening screen
            players = [];
            selectedCard = null;
            votesSubmitted = false;
            document.getElementById('game-board').style.display = 'none';
            document.getElementById('name-input-screen').style.display = 'none';
            document.getElementById('opening-screen').style.display = 'block';
        }

        // Enable enter button when player name is entered
        document.getElementById('playerName').addEventListener('input', function() {
            const enterButton = document.getElementById('enterButton');
            enterButton.disabled = this.value.trim() === '';
        });
    </script>
</body>
</html>
