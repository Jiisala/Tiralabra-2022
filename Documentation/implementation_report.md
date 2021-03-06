# Implemention Report

## General structure

The program launches with launch.py which boots up the UI. The UI is a crude but sufficient text UI. It allows user to manipulate various attributes for the map creation and to output maps. All of the parameters and settings are stored in params.py file. Main engine is in the logic.py file.

When the main engine is initiated, it loads current parameters and prepares an empty map. The map is a 2D array with 1 to indicate walls and 0 for floor. When initiated it will be just full of walls. When UI calls for creation of a map, it calls various algorithms to work together and create the map. Each algorithm resides in its own class. It firs calls the BSP class to create a tree of rect entities. Rects specify an area in x/y coordinates and have up to two smaller child rect entities. It then calls BSP class again, this time to create a list of leaf nodes of the tree. The leaf nodes are used to create a small portion of the map each. For each of them the program calls cellular automata and then flood fill to fill in possible blanks. The program then stitches the map together and depending of the settings, carves tunnels between all of the nodes, outputs the map to terminal and/or outputs it to a txt file (with current settings included)

## BSP (Binary Space Partitioning)

The binary space partitioning algorithm takes a rect entity, that is basically just a rectangular area with up to two child entities, and partitions it to smaller areas, creating a tree in the process. It uses variety of parameters to control the outcome. These are described in greater detail in the ![Manual](https://github.com/Jiisala/Tiralabra-2022/blob/main/Documentation/Manual.md). The algorithm starts with a rectangle same size as the area of the whole map. It then uses recursive splitting algorithm to split the area in two, split both of the halves in two... etc. Until no rectangles bigger than the specified minimum area size remain. Rectangles generated in each level of recursion will be assigned as children to the level above it. The rectangles are always split on the long side, or in the case of square horizontally. The specific point for splitting is somewhat randomized, to create areas of varying height and width. 

There is also some chaos added to force the algorithm to stop splitting before minimum size is reached. For each level of recursion random number is generated between 0 and 99, if that number is smaller than stop_chance that level of recursion will abort. The 1% chance to happen is forced because, for the dungeon generation to work, minimum of one split needs to happen. This will still generate invalid threes, but the logic will reject them later. If all of the trees would be invalid, the program would get to infinite loop.

The find_leaves function finds leaves, by traversing the tree and finding rectangles that have both of their children as None. It uses a depth focused search to traverse the tree and returns an array of found leaves.

Time and space complexity:

The tree can be traversed in O(n) time, with a simple DFS algorithm. 
For the creation of the tree as, far as I know, it is impossible to to give a solid evaluation of the time complexity. That is because the amount of recursion levels for each tree is random. The time complexity could be great for a shallow tree, but horrendous for a really deep tree.  Same goes for the space complexity, as the number of created entities is dependent on a random chance. 

## Cellular automata

Cellular automata uses a bit of a different rule set from the basic Conway s game of life. It counts neighboring walls for each cell in what is called Moore neighborhood, AKA eight adjacent cells. If the cell has more than n neighboring walls it will get converted from floor to wall or vice versa. Different n can be used for wall and floor cells. I found this method to yield aesthetically pleasing shapes. The number of iterations can be controlled also, and will greatly affect the outcome. For most of the settings the algorithm will leave the edges of a area as wall, but that is not always the case. It is also possible to generate only walls or only floors. 

The algorithms starts by creating a map of only walls, then it will go through that map and for each cell determine randomly if it will get converted to floor. Chance of that happening can be adjusted in the parameters. The actual cellular automata part of the algorithm will first make a copy of the map,then go through each cell of the area, count the neighboring walls. Then it will determine the fate of that cell based on the number of neighbors, and if the cell is currently floor or wall. The new values get collected to the copy of the map, and at the end of each iteration changes made will get transferred to the original map. The algorithm will run as many iterations as specified in parameters. Each iteration will generate different result. Generally higher numbers will generate more "soft" looking caves, with less single wall blocks inside them. If number is set too high the results will not change too much compared to lower numbers and performance will plummet. It is advised to keep number of iterations under 10. It is also possible to generate non random predetermined square, and rounded square shapes by setting the parameters right.

Time and space complexity:

The algorithm makes one copy of the array representing the map, and does not really need to temporarily store more data than that. Space complexity is therefore O(nm). Note that for the sake of clarity, I am considering here the 2d array created while initializing the class as the input of the algorithm.
The chocking point of the algorithm is the counting of the neighbors, the algorithm must check at most eight different neighbors for one each square. As the algorithm is going through a 2d array of variable size the time complexity is O(8nm)

## Flood fill

Flood fill algorithm is used here to limit the number of rooms to one per area. As the cellular automata can and often will generate areas with hollow pillars and number of smaller "rooms". Flood fill will count count the size of each separate area and find largest of them. It will be spared and others will get turned back to wall. 

The algorithm does not use any particular changing parameters, as it's behavior is good as it is. It gets a 2D array map and at the end only the largest area is left. When the class is initiated it sets the map given as parameter as self.map, and gets width and height form it. Then find_area function needs to be called. It starts to go through the map from top left to bottom right and when it finds a 0 (floor cell) starts the actual flood fill. A running index number (starting from 2) will be assigned to each area found. The flood fill will start from the found floor cell, mark it with the assigned number and check its neighbors in four cardinal directions. If it finds floor it will add that cell to the queue and add one to counter counting the size of the area. It will then take the last cell from the queue and perform same operations. When the queue is empty it will finish and return the size of the found area. 

If the number returned is larger than the previous largest area the result and the running index number get saved for later use. When the find_area function reaches bottom right corner it will return the index of largest area. Because each area is now filled with its index number the last step is just to go through the map one more time and change the largest area to floor and everything else to wall.

Time and space complexity: 

Space complexity of the floodfill is not easily measurable, but also not really interesting either. The algorithm a couple of integers and a queue that varies in size. The time complexity is O(3nm) as the algorithm traverses 2d array and at worst case scenario visits each cell three times. 

## Corridors

The algorithm carving the corridors is at this moment simple but a bit messy. It takes a tree of rect entities, in this case one generated by the BSP algorithm earlier and traverses it connecting each area with its neighboring area. Because of the recursive nature of the original partitioning, this will guarantee that each area is accessible from every floor cell of the map. It first traverses to bottom of a branch and makes its way back up, linking the areas on its way up.

The actual corridor carving will take two rect entities as arguments and determine if they have been split horizontally or vertically. It then finds the floor cells nearest to the facing edges, if there are several on the same row/column, pics one randomly and marks them as start and end. Then it starts to move, cell by cell from start to end. The shape of the corridor can be somewhat affected by parameters, but they are mainly implemented to make get rid of randomness for testing purposes. The corridor will move horizontally or vertically, depending in the orientation of the rectangles and change direction randomly. It will however always traverse toward the end, and never move past it in either direction. It means that if the corridor reaches the same spot in X or Y axis as the end spot, it will dig straight along the remaining axis without any further turning.

Time and space complexity: 

Time complexity is O(nm) as the algorithm needs to travel the 2d array to find starting and ending spots. Space complexity is some arbitrarily small value, as the algorithm does not really store anything aside the start and end points, if they are found.

## Rng

The random number generator uses system time measured in nanoseconds as seed and a incrementing divider number to assure randomness in different operating systems. From the first implementation without the divider I found out the hard way that windows returns time_ns at lower accuracy than linux. When called it takes lower and upper bounds as arguments, defaulting to 0 and 100 if none are given. The divider is set to 1 on the first call. It then takes the time_ns, increments the divider by one and divides seed by the divider. The final number is a modulo of the seed and the upper bound. If the number is smaller than lower bound, the operation is performed again until a satisfying number is reached. There exists a check to reset the divider back to one if it starts to get too big. This is implemented for the unlikely chance that the program would be left running for extremely long time. In such scenario the divider might grow so big that it breaks the program. Likelihood of this happening is marginal, but the check is there anyway. The rng was tested against the built in python randrange function and it fared sufficiently against it, both in performance and distribution of numbers. The test was performed with range from 0 to 100, it is anticipated that raising the lower bound would cause the performance worsen compared to randrange.

Time and space complexity: 
Since this is a random range function, implemented by getting a number between zero and upper limit and checking it against lower limit, calculating complexities is a exercise in futility. There is a infinitely small but terrifying possibility of never generating the number requested.
