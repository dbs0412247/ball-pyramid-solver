# Ball Pyramid Solver

**This repo is a work-in-progress. Collaboration and constructive comments welcome.**

Figuring out a way to beat this stupid game, brute-force or otherwise.

![Alt text](images/game-overview.jpeg?raw=true "This BS")

# Algorithm

Something that I thought up in ~2 hrs after work.

```cpp
int main(int argc, const char** argv)
{
	GameState initialState;
	
	// TODO: code here to read initialState from input
	
	if ( recursive_solve(currentState) )
}

bool recursive_solve(GameState currentState, PiecePlacement *result)
{
	// Check for done
	if ( isWinState(currentState) )
	{
		result = currentState.getPiecePlacement();
		return true;
	}
	
	// Not done, place next piece and recurse
	bool isDone = false;
	Piece p = current.take_unused_piece();
	while ( NOT(isDone) AND (p != NULL) ) // p == NULL means no more unused pieces to try
	{
		List<GameState> possibleStates = currentState.getPossibleStatesFromNewPiece(p)
		while ( NOT(isDone) AND NOT(possibleStates.empty()) )
			GameState nextState = possibleStates.take();			
			isDone = recursive_solve(nextState);		
		} // end while
		
		p = current.take_unused_piece(); // loop advancement
		
	} // end while
}

// More TODO

```

# General Ideas

* The game "board" has 5 horizontal, square layers, stacked on top of each other to form a square-based pyramid. 
	* Layer 1 has `1` ball (slot)
	* Layer 2 has `4` balls
	* Layer 3 has `9` balls
	* Layer 4 has `16` balls
	* Layer 5 has `25` balls
* Hence, the "board" is a connected graph, each node (or slot) is a position for possible ball placement. Therefore, there are `1+4+9+16+25=55` total nodes.
* Nodes can be either occupied or unoccupied (empty)
* If each ball has radius of `1`, then every connected node pair in the "board (graph)" is has a distance of `2` apart.
* Hence, any node in layer N is:
	* connected with `4` nodes in layer N+1
	* connected with `2`, `3` or `4` nodes in layer N
	* connected with `1`, `2` or `4` nodes in layer N-1
* Vertical distance between the centers of the balls on adjacent layers is `sqrt(2)`, which also equals the distance between any diagonally adjacent balls on the same layer.
* The sum of all balls in all pieces equals the number of nodes in the graph.

# Solve Tree Pruning Conditions and Heuristics

* if there are more connected components of empty nodes in the graph than pieces remaining, then this is a failed state (i.e. cant break apart pieces)
* check for remaining piece geometry for early exit
	* longest straight can't fit into available space
	* smallest connected component is smaller than smallest remaining piece
	* remaining slots are long and narrow but there is a wide piece remaining