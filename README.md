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

## Heuristics Analysis
I started the heuristics portion of the project by going through test runs *(game.play())* with my *AlphaBetaPlayer* and the provided *GreedyPlayer*. An observation I made was that it was an invariably bad decision for a player to move to a corner, especially later in a game. It was even bad at times to be caught along the perimeter if a partition was forming because it severally limited the number of legal moves available to the user. It seemed wise to stay away from the perimeter and the corners for as long as possible.

## Heuristic 1
One of the lecture videos for this section discussed the effects of the formula, my_moves - 2 * opp_moves and how it caused the computer player to play more aggressively. I was intrigued by the idea of this and wondered what the results of a tag-like game style would be. Where one player would play aggressively while the other played defensively for half of the the game and then the two switched strategies. I tested both starting aggressively and defensively for the computer player to see which produced better results and it seemed that it was better to start off defensively and then finish the game aggressively (see Figure 1 and Figure 2).

```python
my_moves = len(game.get_legal_moves(player))
opp_moves = len(game.get_legal_moves(game.get_opponent(player)))
blanks_spaces = len(game.get_blank_spaces())
n = 3
m = 1

# If half of availble moves on the board are filled, switch strategies
if blanks_spaces <= (game.height * game.width) / 2:
    m = 3
    n = 1

# The computer player will start playing defensively and then switch to playing aggressively towards the end of the game.  Visa-versa for the opponent
return float((my_moves * n) - (opp_moves * m))
```
Figure 1 - Aggressive and then defensive - difference: 5.8%
![figure 1](analysis-img/fig-1)

Figure 2 - Defensive and then aggressive - difference: 7.1%
![figure 2](analysis-img/fig-2)

## Heuristic 2
The main idea behind this heuristic was to penalize the computers moves that were located on the corners of the board and reward the opponent moves that were located on the corners. As previously stated, I noticed when during my trial runs of printing out the end result of game board, I noticed that corner moves often led to loses.
I tried two different techniques for this heuristic, one where I checked if the players current position was located on a corner, game.get_player_location(player), and for the second I checked the number of future moves that were located on a corner. I had better results with the latter which is what you will see below.

```python
my_moves = game.get_legal_moves(player)
opp_moves = game.get_legal_moves(game.get_opponent(player))

# Locations for all 4 corners
board_corners = [(0, 0), (0, (game.width - 1)), ((game.height - 1), 0), ((game.height - 1), (game.width - 1))]

# Number of my available moves located in corners
my_in_corner = [move for move in my_moves if move in board_corners]
# Number of opponents available moves located in corners
opps_in_corner = [move for move in opp_moves if move in board_corners]

return float(len(my_moves) - (2 * len(my_in_corner)) - len(opp_moves) + (2 * len(opps_in_corner)))
```

## Heuristic 3
Here I am taking the difference between the computers available moves and the opponents available moves and then adding their difference in distance from the centre of the board. A board state where the computer has more moves available and is located closer to the centre of the board than the opponent will be rewarded v.s the alternative.

```python
my_moves = len(game.get_legal_moves(player))
opp_moves = len(game.get_legal_moves(game.get_opponent(player)))

# Game board width and height
w, h = game.width / 2., game.height / 2.

# Computer and opponent player coordinates
y, x = game.get_player_location(player)
opp_y, opp_x = game.get_player_location(game.get_opponent(player))

# Computer and opponent distance to centre
distance_to_center = abs(w - y) + abs(h - x)
opp_distance_to_center = abs(w - opp_y) + abs(h - opp_x)

return float((my_moves - opp_moves) + (opp_distance_to_center - distance_to_center))
```

## My recommendation
After running 100 matches, heuristic 3 seemed to be most effective, given that it beat AB_Improved by 2.9%. I think the reason it beats AB_Improved is because it takes two variables into its equation, the difference in the number of moves between the 2 players and the difference in distance from the centre for the 2 players. Both of these variables are vitally important to winning the game of isolation.
