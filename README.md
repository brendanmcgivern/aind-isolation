# Game-playing Agent - Minimax
![isolation visualization](viz.gif)

## Synopsis
In this project, I developed an adversarial search agent to play the game "Isolation". Isolation is a deterministic, two-player game of perfect information in which the players alternate turns moving a single piece from one cell to another on a board. Whenever either player occupies a cell, that cell becomes blocked for the remainder of the game. The first player with no remaining legal moves loses, and the opponent is declared the winner. These rules are implemented in the isolation.Board class provided in the repository.

This project uses a version of Isolation where each agent is restricted to L-shaped movements (like a knight in chess) on a rectangular grid (like a chess or checkerboard). The agents can move to any open cell on the board that is 2-rows and 1-column or 2-columns and 1-row away from their current position on the board. Movements are blocked at the edges of the board (the board does not wrap around), however, the player can "jump" blocked or occupied spaces (just like a knight in chess).

Additionally, agents will have a fixed time limit each turn to search for the best move and respond. If the time limit expires during a player's turn, that player forfeits the match, and the opponent wins.

## code

This project requires python 3.  I personally use Anaconda, so I have provided a **aind-universal.yml** file at the root of the project to create an environment with all of the required dependencies.

```bash
bash$ conda env create -f aind-universal.yml
```
OSX & Linux
```bash
bash$ source activate aind
```
Windows
```bash
bash$ run activate aind
```
To run the application
```bash
(aind) bash$ python tournament.py 
```
To run the applications unit tests
```bash
(aind) bash$ python -m unittest -v
```

## Process and Challenges
Throughout this project I implemented the **Minimax** algorithm, **Alpha-beta pruning**, **iterative deepening** and three different heuristics in an attempt to outperform the test based heuristic: **(my_moves - opp_moves)**.  I was surprised to see how difficult it was to come up with a “good” heuristic. What I came to realize was that it’s particularly difficult to beat a fast heuristic. I think the reason a fast heuristic is so difficult to beat is because speed plays a big factor in a game like this, where a time constraint exists and a simple heuristic allows the search algorithm to search deeper in the tree than a more complicated heuristic.


