# Testing report

For testing of the program three main approaches were taken. Automated Unittesting to make sure that everything works technically as intended, performance testing to get general grasp of the performance of the different parts of program and vigorous manual testing to assess the aesthetic outcome of different parameters. Because quite a bit of randomness is involved in the process, multiple passes of the same settings are needed, to get a picture of what kind of dungeons they generate. Since the main focus of the program is to generate usable dungeons, aesthetics were chosen over performance when the situation called to choose one or another.

## Testing methods

Unit tests are written for all of the classes, except UI. The tests for logic class concentrate mainly on testing that calling of the other classes work as intended. The functionality of the algorithms has been already tested in the tests written for the respective classes. For other classes the tests aim to test that the classes perform as wanted with given parameters and that no unwanted side effects rise from them. Testing should cover most of the branches in the code.

Performance tests are run on each algorithm separately and for the whole program. They are all written in performance.py. They consist of running the algorithms with different parameters, stripped of randomness as far as feasible, and measuring the running time. They are easily repeatable at will just by running performance.py. 

The manual testing is performed by running the program multiple times, changing the parameters after sufficient amount of runs and writing the results to a file. The file will then be inspected by a human inspector and observations of the results will be made. The file will get uploaded to the repository for others to inspect.

The quality of the code is tested with pylint. Some exceptions are allowed, for example I really think that "x" and "y" are acceptable variable names, even if they dont strictly adhere to pylint standards. The final score is 9.63/10. The errors left are errors that I chose to leave there as I felt that fixing them would complicate the code rather than make it better.

![pylint](https://github.com/Jiisala/Tiralabra-2022/blob/main/Documentation/img/pylint.png)

## More on Unit testing

In the final version branch coverage is 96%. Only tested class with less than 100% coverage is the logic class, it's coverage is 86%. The missed branches are all but one related to error handling. They prepare the class for failure of one of the algorithms called. I implemented them guite early in the developement, and at this stage, could not trigger them with tests. I did not want to write fake tests to cover them, but was also too afraid of getting rid of them at this stage, just before final deadline. I chose to leave them as they are, and leave them visible for coverage report. If I later continue to tinker with the program it is better that I can detect things like that easily (and for some reason code with "# pragma: no cover" markings confuses and infuriates me). The test for displaying map on screen misses the branch of printing ".". I decided that figuring out a way of getting it covered was not worth the trouble. If that functionality would somehow break, it would be basically impossible to not notice it when running the program for the next time.

![coverage](https://github.com/Jiisala/Tiralabra-2022/blob/main/Documentation/img/coverage.png)

## More on performance testing

Performance tests are done by running each algorithm one thousand times and measuring the spent time. While not too great number of iterations for performance testing one thousand times, is enough to note how different parts of the program perform in relation to each other. The measured times given bellow, are gathered from test running on a "freshman laptop 2021 edition", running cubbli linux. It has the following specs:

- Lenovo ThinkPad T14 gen 2
- 4-core processor, 8-thread Intel Core i5-1135g7, 2.4-4.2 GHz
- 16 GB main memory
- internal flash disc (NVMe-SSD) 512 GB

The relevant preferences used when running the tests are:

- file_name = "performance.log"
- output_to_console = 0
- output_to_file = 0
- map_height = 100
- map_width = 100
- floor_propability = 0
- iterations =1
- stop_chance = 0
- turn_chance = 0
- stop_partitioning_height = 99
- stop_partitioning_width = 99

These are devised so that they would strip the program from randomness, as much as it is possible to do via preferences. Some randomness remained, but It should not affect the running times too much. BSP was tested twice, first with a shallow tree of two leaves, and then with a tree of four leaves.

One test that was not strictly a performance test, but was tested at the same time, was a test to see that the RNG I wrote gave roughly similar distribution than the pythons build in randrange function. That was achieved by running both of them one million times and noting how often each number appeared. Then the subtraction of the number with least appearances and the number with most appearances was calculated from both. From that it can be concluded that the RNG is sufficiently random. The floodfill algorithm has a recursive function to traverse the BSP tree and look for leaves, but it is essentially the same as the BSP traversal function, so it is not tested separately. The corridors test involves initiating the matrix used for testing before each iteration. Time spent on that is fraction of the whole test time, so I did not feel like subtracting that from the time it took running the test necessary

The performance of the program was much worse than the individual algorithms combined. This is seems to be mainly overhead from stiching the small maps together. That part of the program could probably be optmized quite a lot, but for now I will let it be as it is.

The results of the tests run before writing this were as follows: 

- BSP shallow tree

   0.004816770553588867 seconds 
- BSP deeper tree
 
   0.010364055633544922 seconds 
- BSP traversal
 
   3.24249267578125e-05 seconds 
- Cellular automata
 
   5.1975250244140625e-05 seconds 
- Floodfill 

   0.42391252517700195 seconds 
- Corridors 

   0.3053302764892578 seconds 
- RNG peformance 

   0.0005633831024169922 seconds 
- RNG distribution mine (diference of most common and least common number) 

   1677 
- RNG distribution python (diference of most common and least common number) 

   1830 
- The whole program ran 1000 iterations 

   43.75894284248352 seconds 

By Investigating the result we can see that the BSP algorithm, cellular automata and RNG perform at much faster pace than Floodfill or corridors. For floodfill that is expected, because it potentially needs to visit each cell of the map multiple times. There are versions of the algorithm that are optimized better than the one used here. The different algorithms used are of course not directly comparable with each other, in terms of performance, but the test shows clearly which of them would need to be optimized most urgently if better performance was sought. The number for cellular automata is lower than expected. I did not factor in the original creation of the array. It seems to affect the outcome more than I thought, even if it is created only once vs the 1000 times the algorithm is ran. This became more evident when testing with bigger array dimensions. There probably is something in how python handles arrays that I don't know explaining the situation, or there is something fishy with my testing method.

# Manual testing

Through out the developement vigorous maual testing has taken place. As the aim of the program is to generate usable and above all aesthetically pleasing dungeons, testing the different settings and seeing that everything works as intended has been valuable tool. Most of the testing has not been recorded, but, to show an exaple of the process, I wrote a testing session to a log file that can be inspected later. I uploaded the log file from a recent manual testing round to the repo. it can be found from [here](https://github.com/Jiisala/Tiralabra-2022/blob/main/data/test.log). It is a 34182 lines long file, so I won't paste it here, but feel free to browse it at will. I will however pick some examples and insights and present them bellow. Note that after these tests were made, the corridor generating algorithm was slightly changed.

Many of the different maps would benefit from different color schemes, but here they are presented as they come out of the program. I hope that the reader can use the power of imagination to add color. The log file is by no means a full collecton of different possibilities, but should give the observer some insight in how the settings affect the maps.



A sample of divided areas, with the cellular automata set to just carve out rectangular rooms as big as possible. The placement of corridors is different in the current verson.

```bash
GENERAL:
            1. Map width: 50
            2. Map height: 30

            BINARY SPACE PARTITIONING:  
            3. Minimum sub area width: 6
            4. Minimum sub_area_height: 6
            5. Stop chance: 0
            6. Width to cease partitioning: 12
            7. Height to cease partitioning: 12

            CELLULAR AUTOMATA:
            8. Initial floor propability: 0
            9. Min neighbouring walls to set wall to floor: 6
            10. Min neighbouring walls to set floor to wall: 3 
            11. Iterations: 1  

            CORRIDORS:
            12. Chance to make a turn on each step: 50
            13. Draw corridors 1 = yes 0 = no: 1

            OUTPUT:
            14. Output to console: 1
            15. Output to file: 1
            16. Path to file: ./data/
            17. Filename: testi.log
            
##################################################
#................................................#
#......##......##.........##.......##.....##.....#
#......##......##.........##.......##.....##.....#
#......##......##.........##.......##.....##.....#
#......##......##.##########.......##.....##.#####
#......##......##.##########.###############.#####
#......##......##.........##.###############.....#
#......##......##.........##..............##.....#
#.###############.........##.......##.....##.....#
#.###############.........##.......##.....##.....#
#..............##.##########.......##.....##.....#
#....##........##.##########.......##.....##.#####
#....##........##.........##.########.######.#####
#....##........##.........##.########.######.....#
#....##........##.........##.......##.....##.....#
#.###############.........##.......##.....##.....#
#.###############.........##.......##.....##.....#
#..............##.........##.......##.....##.....#
#.......##.....##.........##.......##.....##.#####
#.......##.....##.##########################.#####
#.......##.....##.##########################.....#
#.########.######.........................##.....#
#.########.######......##.........##......##.....#
#.......##.....##......##.........##......##.....#
#.......##.....##......##.........##......##.....#
#.......##.....##......##.........##......##.....#
#.......##.....##......##.........##......##.....#
#.......##.....##......##.........##......##.....#
##################################################

```


Similar settings with smaller area sizes creates a map maybe fit for a cellar level, or prison like dungeon.
```bash
GENERAL:
            1. Map width: 50
            2. Map height: 30

            BINARY SPACE PARTITIONING:  
            3. Minimum sub area width: 3
            4. Minimum sub_area_height: 3
            5. Stop chance: 0
            6. Width to cease partitioning: 6
            7. Height to cease partitioning: 6

            CELLULAR AUTOMATA:
            8. Initial floor propability: 0
            9. Min neighbouring walls to set wall to floor: 6
            10. Min neighbouring walls to set floor to wall: 3 
            11. Iterations: 1  

            CORRIDORS:
            12. Chance to make a turn on each step: 50
            13. Draw corridors 1 = yes 0 = no: 1

            OUTPUT:
            14. Output to console: 1
            15. Output to file: 1
            16. Path to file: ./data/
            17. Filename: testi.log
            
##################################################
#................................................#
#.##.##.####.####.###.####.###.##..##.##...##.##.#
#.#####.####.####.###.####.###.##..##.############
#.#####...##...##..##...##..##...####.############
#....##...##...##..##...##..####.####............#
#.##.##...##.###################...##.##..##.##..#
#.##########.####################.###.##..##.##..#
#.##########.......##############.###.######.#####
#.........##.###.#############.....##....###.#####
#.##..##..##.###.#############.##..#####..##.....#
####..##..##..##...###########.##..#####.######.##
####.#######.###########################.######.##
####.#######.###########################..#####..#
####......##.....####..............#####.#########
####.##...##.##.......###.##...##.######.#########
####.##...##.#####.######.##...##.###............#
####.#######.#####.....##.#######..##.##.##.##.###
##...#######....##.##..##.#######.###.##.#####.###
#.........##.##.##.##..##......##.###.##.#####...#
#.##.##...##.############..##..##..##.##.#####...#
#.##########.############.###########.##.#####.###
#.##########...........##.###########.##.#####.###
#.........##.##.####...##..........##.##....##...#
#.##.##...##.##.####.####..##..##..##.##.##.##...#
#.#####.####.##...##.####.###########.############
#.#####.####.##.####...##.###########.############
#....##...##.##.####...##..........##............#
#.##.##...##.##...##...##.##.##.##.##.##.##..##..#
##################################################

```


Setting the initial floor probability the low side, and iterating the cellular automata just once generates few small rooms connected with long corridors

```bash
  GENERAL:
            1. Map width: 100
            2. Map height: 50

            BINARY SPACE PARTITIONING:  
            3. Minimum sub area width: 6
            4. Minimum sub_area_height: 6
            5. Stop chance: 0
            6. Width to cease partitioning: 40
            7. Height to cease partitioning: 40

            CELLULAR AUTOMATA:
            8. Initial floor propability: 30
            9. Min neighbouring walls to set wall to floor: 6
            10. Min neighbouring walls to set floor to wall: 3 
            11. Iterations: 1  

            CORRIDORS:
            12. Chance to make a turn on each step: 50
            13. Draw corridors 1 = yes 0 = no: 1

            OUTPUT:
            14. Output to console: 1
            15. Output to file: 1
            16. Path to file: ./data/
            17. Filename: testi.log
            
####################################################################################################
####################################################################################################
####################################################################################################
####################################################################################################
#############################################################################################...####
#############################################################################################...####
#########################################################################.......................####
#################################.#######################################.####################..####
#################################.######################################..####################...###
#################################...####################################.######################..###
#################################.#####################################..######################.####
#################################.####################################..############################
#################################.###################################..#############################
#################################....................################.##############################
#################################.###############..#..##############..##############################
#################################.#############....##.##############.###############################
#####.###########################.############..#####..########...##.###############################
#####..##########################.#############.######...........##..###############################
#####...#########################.#############.##############...##.################################
#####....########################.#############.###############.....################################
####....#########################.############..###############....#################################
####.############################.###########..################.....################################
####..###########################.########....#####################.################################
#####..##########################.######...########################........................#########
######.##########################.##.....##############################################..#..########
######.##########################.....################################################...##.########
######........###################.#....############################################.....###.########
#############...#################.####.############################################.#...###.########
###############.....#############.####################################################..###.########
###################.#############.####################################################..###.########
###################....##########.###################################################..####.########
######################.##########.#########################################################.########
######################.##########.#########################################################.########
######################.#########..#########################################################.########
######################.#######...##########################################################.########
######################.##......############################################################.########
######################.##.#################################################################.....####
######################....#################################################################.......##
##################.#...####################################################################..#.....#
#################......###########################################################################.#
################.......#############################################################################
###############...###.##############################################################################
###############..##.################################################################################
###############.....################################################################################
###############.#....###############################################################################
###################..###############################################################################
####################################################################################################
####################################################################################################
####################################################################################################
####################################################################################################

```

Setting the floor probability on the high side and wall to floor transistion thereshold low, creates a map suitable for overworld generation, could easily represent a forrest, and be used as tiles to get a arbitary sized map. Raising the initial floor probability genereates less dense forrest. 

```
GENERAL:
            1. Map width: 100
            2. Map height: 50

            BINARY SPACE PARTITIONING:  
            3. Minimum sub area width: 6
            4. Minimum sub_area_height: 6
            5. Stop chance: 0
            6. Width to cease partitioning: 40
            7. Height to cease partitioning: 40

            CELLULAR AUTOMATA:
            8. Initial floor propability: 70
            9. Min neighbouring walls to set wall to floor: 2
            10. Min neighbouring walls to set floor to wall: 3 
            11. Iterations: 1  

            CORRIDORS:
            12. Chance to make a turn on each step: 50
            13. Draw corridors 1 = yes 0 = no: 1

            OUTPUT:
            14. Output to console: 1
            15. Output to file: 1
            16. Path to file: ./data/
            17. Filename: testi.log
            
##.##......####..#...........##..............#####...#...#..#...###.#................#.##.#.##......
...##....######............##.....#..#.......######....###....##......##.............#......#....##.
.....##...####...###.....#.......#######.#...#######.....#....##.......##..#...#.#...............###
#..#.......#.##....#...#.............##......###...#.#...........#....#.###.#.......#...........####
.#..##.........#.##...#.#....#.......###.....##....#..#.....#.#......##...........#..#..##..##...#.#
.#..####.##..#...#............#...##.###.#.#.##...#....#..##.#...........###.........#...#...#...#..
.....#......#..........#..##.#..#....##...#..###...#.#.............##....#.....##............#.#....
.......#..........#...#.#.##...###....##.......##..#....#.#...###................#..####.........#..
.#.....#..##.####.#......#....#####..#.###.....##.....#.....#...#...#.#......#......#..........#....
...#......###........##.......###......#...#...###.......#....#......#.##.#.#.......#.##.#.##....#..
.#.......#...#.............###..#.##..##.#.##...#.#..#..###.......#.....###..##.....###..........##.
#.#.....##.#..#.#.#....###......####..........##.....##..........#..#..#...#.#....####....#...#....#
....##...#..........#..##.###...........###......#...##.....#.......#.##.#.#....#.####.#.##.........
..#........#.#..#...#..##..###.....#.##..#..#....#.#..#..#..#.#....#.#..##.##..##.##..#####.........
.#........#####......#.##.......###..###...........#..####.....#.....#..#.......#..#...###..###.#..#
.###..#...#########...#..#####....###..##.##.#....#...#.#.#...#........#..#....#.#.#....#..###..#...
.......##..########..###..#####...##..........#......#................###.#.........#.#.#.#......#.#
.......##..########...#.#.######....##......#.#....##.##..#.#.#.#...#....##.....###...........#.....
.##.....#..########.#......######...###..#..####.#.........#......###.........#...#..##.###....#.##.
#..#......#.#######....##....#####...###.#..#####...###.#..#...#..#...............#..###.###...#..#.
...#..#.#.#.....###..#..##.##.#.......##....#####.##..##...#...#.###......#.##......###.....#...##..
.#......#....#...#..###.#............##...#######.#....##.........#.#.........#.##.####..#.##..#.#..
.......#..#...#.......#.##....#...###.......######..##....##.#.#.......#...#...#######..#..#.....#..
.##..#.#.....###...........#..#..##...##....######.###.....#..#...#....###......####..#.##......#...
.#...........###......##.##....#.####......#######.###..#..##...###.#######...######....##.........#
............#####.....#..###...######....#..###########.#.##....###.........##....##....#..#...#####
##.##.###.....######.#..###.##..###..#.....###########.......#..#######..#...#.#...#..........######
...#..###.#.#########....#...#...#......#...#########........#...#####...#...............##...#.####
#...#.######..###.####......###..##.........#########.##....#..#.####...#.#.......#....##....#..#.##
##.....###.....#...##..#..####......#..##.#################......#.##...#......#...#........#...#.##
..#....####......#...#....####....#..###################....#...#......##....#...#....#....#.#....##
........####....#..#...##..#.....#..#.#.##.#############...###.##...........#..###..#.............##
#.......#..#.......##.....#......#..........##...........########...........##.######..#..#...##.#..
#..#....#.#....#...##...#.#..#.##.....#.##..#...#.#...###########..........#...#.####.########......
##...##.....##.....#..##....###..#..#...###.....#..#.#.###########...#...#..............#####....#..
###.####...#...#...#######........##........#.......#...#####..##.#.#.##...##.......#.#....#.....###
##..####.#..#...#.####.........#######..#...............######..#.....#....#..##....#...#.....#.####
##.#####..#..#.###....#..####.####..#.....##..#..#..#.#..######.......#..####....#.##.#.........####
....#####..#..........##..###..##..##....#.#..#.##..#.#...####...#........##.....#.#....#.....#..##.
.#..###............#..##..###.###..###.#....#.#..........###...###.....###...........#....#.#.......
#..#.#..#.....#....#.##..###...#..##.........###.#...##.#.#....##....###...#...#.####....###..#....#
...#..#..........#.#..#....####....#...#.....###...##..#.....####....#####..#......#.#...#...#....##
..#....#........#...#.......##..##.##...#.....####.##..###...####.....###.....................#...##
....##...##.....#....#...###.....#......#..##..#.#.....##...######....##....#.#.....#...##..#.......
...#.......###...........####.##.##....###............###....#.....#.#.#....#...#....#..........#.#.
.##...##..................#######.....#.........##.##.....#.#..#..#....#...#....#..#..##..#..###....
##.....#...#......#..#.#.#...##..#....#.....#.##..........#..#.....#.#.#......#.#...#......##.##....
###.#.##......#.....#...#.....#.##....###..########.###..###....#...#.#.###.............#...#..#.#.#
####.#.#.........#..#......#.##......####..#######....#...#.........#....######..#.......#..##..#...
####........#..#.#.#...#..#.....##..####..#########......###..##.......########....####...#..#....#.

```

The "basic" settings that generate visually pleasing dungeons. To get more variation, stop chance can be set higher.

```bash
        GENERAL:
            1. Map width: 100
            2. Map height: 50

            BINARY SPACE PARTITIONING:  
            3. Minimum sub area width: 6
            4. Minimum sub_area_height: 6
            5. Stop chance: 0
            6. Width to cease partitioning: 40
            7. Height to cease partitioning: 40

            CELLULAR AUTOMATA:
            8. Initial floor propability: 40
            9. Min neighbouring walls to set wall to floor: 6
            10. Min neighbouring walls to set floor to wall: 3 
            11. Iterations: 3  

            CORRIDORS:
            12. Chance to make a turn on each step: 50
            13. Draw corridors 1 = yes 0 = no: 1

            OUTPUT:
            14. Output to console: 1
            15. Output to file: 1
            16. Path to file: ./data/
            17. Filename: testi.log
            
####################################################################################################
###########################..........#####.#######.###########.......#####....##############..######
##########################............###....####...####...##...................#####..####....#####
##########################.....###....####....###....###........................###.....##.....#####
##########################....#####...####............##.###........................#.##........####
##########################....######...##................###...........#.......##....###........####
##########################.....#####....................####............###....##.#...#......#...###
##########################......####....................####...###......#####.###.##..............##
##########################......###..........#......##.#####...###.#.....########.###..............#
##########################......##.................#########...##........########.###..............#
##########################............###..........#########...##........####.###.###.........#....#
##########################...#....#######......##...########...###..#....###...##.#######.........##
##########################........####........##############...###.......##....##.#######.........##
##########################........####........###############..###.............##..#####..........##
##########################.#......####......#.####################.............##....##...........##
######################.#...##.....#####........######.############.............##..................#
#####.###############....####......#######.....####...############.............##...........#......#
#####...#############..######.......######......##...#########.##..........##..##....##...........##
######....########....######.........#####..........#########.............###..###.#####..###....###
####.......##.####....#####..........####...........#########.............###.#################.####
##.............###....#####..........####..........##########.....#.......##..######################
##.........#...........####.....###.########.......##########.#...............######################
#.........###...........###..###############......###########...........#.....######################
#..........#............###..############....#....###########..................#####################
#......................###......########........############...........##..##..#####################
##..............##########.......########......####.########...........##..##..#####################
####...........###########...#############..#.#####..#######..##......###......#####################
#####.........############..#########..###...#####....######..##......###.....######################
#####........#############..##########........###.....######..##......##....########################
#####........#############...###..####.................#####..........##...#########################
#####................#####........#####.....#...........####..........##...#########################
#####.....##...........###.......#######.................####.........##....########################
####.....####...........##......#########................#########..#####...########################
###......####...........###.....#########................################...########################
###.....######.........#######...########................#################.#########################
###....########################.##########.########.....#################..#########################
######..####################....######################################....##########################
#######...........####.#####.#################################..........####################.....###
#############...........###..########.#######......###########.............###########..##........##
#############...........###...##.##....#####........####.#######.........#..#########...##........##
####################....###.............####........###..#######.........##.#########...##........##
####################....###..............###............########...#.....##.............##.........#
####################....###..####.......#####...........#######..........###########...........#..##
##########....####......###..#####....#######...........######..........#############.............##
#########...............##....#####...######.....###....#####......###..#############.............##
########...###......#...##....#####...####.......###....########...##################.............##
###..##.....###.....##..##.....####..............##.....########...##..##############.............##
###.........####.....#..##......#####.....#####.###.....########........##############............##
####........#####...#######....########..#############.##########......################..####.######
####################################################################################################

```

Low initial floor probability, mid wall to floor thereshold and high floor to wall thereshold, generate maps suitable for insides of a crumbling factory complex. If area size would be set lower, could be used for residental buildings. Setting floor to wall thereshold to 9 generate maps even more destructed walls, but still recognizable room shapes.

```bash
 GENERAL:
            1. Map width: 100
            2. Map height: 50

            BINARY SPACE PARTITIONING:  
            3. Minimum sub area width: 6
            4. Minimum sub_area_height: 6
            5. Stop chance: 0
            6. Width to cease partitioning: 40
            7. Height to cease partitioning: 40

            CELLULAR AUTOMATA:
            8. Initial floor propability: 9
            9. Min neighbouring walls to set wall to floor: 5
            10. Min neighbouring walls to set floor to wall: 8 
            11. Iterations: 1  

            CORRIDORS:
            12. Chance to make a turn on each step: 50
            13. Draw corridors 1 = yes 0 = no: 1

            OUTPUT:
            14. Output to console: 1
            15. Output to file: 1
            16. Path to file: ./data/
            17. Filename: testi.log
            
########.###.######.#############.##########.#############.##.#####.#####################.##########
#....................#.##.............................####......##..........................##.....#
#......................##.............................###.......##................................##
.......................#..............................##........##....................#.#.........##
#........#.............#......#..................#....##........##......................#..........#
#...#..................#.......................#.#....##........##......#.#........................#
#...............##.....#.........................#....##........##.................................#
#................#.....#.......................#......##....#...##......##.........................#
#.................#....#..............................##........##.....................#...........#
#...............................................................##......#..................#...#....
##.....................##.............................#.........##.............................#...#
.......................##.............................#.........##.................................#
##.....................##.............................#.........##..................#..............#
#......................##.............................#............................................#
#......#................#.......................#.....#.........#.......................#......#...#
#.......#..............##...#........#..........#.....#.........#.....................#.#..........#
#......#...............##..#..................##......#.........#............................#.#...#
#......................##...##..................................#...................#.#............#
#..#...................##.............................#...............................#......#.....#
#.#..#...........#.....##....#........................#........##....................#..............
#...#............#.....##.............................#.......#.#..................................#
#......................##.............................#.........##.................................#
#......................##....................#........#.........##.................................#
#......................##.............................##...#....##....##...........................#
#......................##.............................##..#.....#####..###.######.#######.####.##.##
#......................##.............................##........#####......######.#########.########
#......................##........#....................##.........#...........#............#....#.#.#
#......................##......................#......##........##.............#........#......#...#
#......................##.............................#.........##...............#......#.#........#
#......................##....................#.#......##........##.............#.#.................#
#......................##......................#......##........##.................................#
#................#.....##.............................##........##.................................#
##.....................##..........................#..##........##.................................#
##.....................##.........................#...##........##..............##.................#
#..................#...#############.############.##.#############.................................#
##################.#################..................############...............#.............##..#
##.................#.#.##.......##.......................#......#.........#........................#
#......................##.......##..........###.................##.............................#.#.#
#......................##.......##..............................##.............................#.#.#
#.......................#....................#..................#..................................#
#......................#........#...............................##.................................#
.......................##.......#.............................####..................................
#......................##.......##..............................##..................................
#......................##.......##........................#....##...............#..................#
.......................##.......##..............................##........#........................#
#.......................#.......##..........#.............#.....##........#.#.##...................#
#......................##.......##..............................##.................................#
#......................##.......##..............................##...........#................##...#
#......................##.......##...............................#...........#.....................#
#####..#########.##.##.########################################.#############..###.############.####


```


These particular settings create a lot of repeating circular patterns. Useful for creating ocean floors or really alien looking canyons. Floor probability of 65 seems to be a sweet spot for this. 

```bash
 GENERAL:
            1. Map width: 100
            2. Map height: 50

            BINARY SPACE PARTITIONING:  
            3. Minimum sub area width: 6
            4. Minimum sub_area_height: 6
            5. Stop chance: 0
            6. Width to cease partitioning: 40
            7. Height to cease partitioning: 40

            CELLULAR AUTOMATA:
            8. Initial floor propability: 65
            9. Min neighbouring walls to set wall to floor: 6
            10. Min neighbouring walls to set floor to wall: 5 
            11. Iterations: 3  

            CORRIDORS:
            12. Chance to make a turn on each step: 50
            13. Draw corridors 1 = yes 0 = no: 0

            OUTPUT:
            14. Output to console: 1
            15. Output to file: 1
            16. Path to file: ./data/
            17. Filename: testi.log
            
##....##...##...##..##.#....#..#.#..#..#..#..###..#.#..###..#..###############################......
##...#..#..#..#..#..#....##..#...#...#...##...#..#..##..#...#..###############################.#.#.#
###..#.#..###.#......#.###...##....###.......#..#....#....##....#################################..#
###.#......#.#.#....#..####...................#......####.........################################.#
###..#..#.#..#...#..#..###.#####.......###........................###############################..#
##..#...##..##...##....###...##...##...#...#....####......##.....################################...
##....###.......####....#...#..#..#..#..#..#..#...##...##...#...#.##############################...#
.........................#..#.###..#.#.###......#..#...#..#..#..#.##############################....
#####......###....###.......#.###.#.#..###....#..#..#.#..###.#....##################################
###...##...#...#...#..##..#.#..#..#..#..##..#.###.#.#.#.###......###################################
####..#..#..#..#.#..#.####..##..##...#....##...#....#.#..#..#.#..###################################
###.#....#.#.#.#.#.########.....###.......####..#..##..#..##..#...##################################
###.#....#.#....#..##########........##########...##...##....##...########################......####
##..#..#..#..#...#..###.......####.....#######..##.......####.....#########################.....####
###..##...##....##....##...##...#...##..######............#.......########################...##....#
##.....####............##..##..#.#..#..#######.#####......###....#########################....#...##
.........#..........#####.#.#....#.###.#########...##...##...#...#########################..#...#..#
#......###....##....#####.#....#.#.###########...#..##..#..#..#..#########################..##..#..#
###...#...#...#..#...###..#.##..#..###########..###..#....###..#.#########################.####..#.#
##..#..#.#..#....#.#..##...##..##...##########.#.###.#.#.#####.#.#########################..###..#.#
##.#........#........##....###......##########....###..#.####....##.....##################.#.#......
#.#....#.#..#....#..####.......##########.......#..#..##..#####..#...#...#################.......#.#
#..#..#...#..#...#...##..###.....###...##...###..##...##...##....##.#..#..################.#.#..####
...##...##....##.....##...##..##..##..#..##..##.....####.......####.#.#.#.#####.########.#...#..####
##........#############..#..#..#.#..#.#..###..######........########..#....####..#######...#..#..###
#########.........#####..#.###.#.......#.###.####........###.....##...#..######...######..#...#..###
#######...##...##...###.#..###.#..#....#..#..##....##...##...##..#..##....###......####...##########
#######..#..##..#..####..#..#..#...#...#...#..#..##..#..##..####..........................##########
###.#..#.#..###..#.####.##...##...###....###..#..###..#....#.####.....####......##.....##.##########
###.........###.#######.....####..............#.#.#...........###...#######...#...#...#..###########
###############...........###....##....########.#....#.#..#.#..#.....####..#..#.#..#..#.############
##########....##...##...#...#...#..##..########..#..#..#..#..##..#...#####....#.#.###.#.############
########...#..##..###..#..#....#....#.####.####..##..##...##....##...#####....#....#..#..###########
#######..#....#..#.##.#.#.......#.#.#.###..###....###......####....#..#####..#..##..#...############
########.#.........##....#..#.#..#..#..#..####......................#######....##.....##############
#####.#..#.#....#..###..##..#...##...#....####.......####......###.....#####..............##########
####..#..#..#...#..##..##.....###.......######....##....##...#...#...........#####......#########..#
###...##...#....##...#..............#######...#..##..#...#..#..#..#.....##....#....#...##.#######..#
###.....###........##....###.....###....#....##..#..#..#.#..#.#...###..#...#..#..#..#..#..######....
#####............#####...#...#...#..##..#..#...#.#.#.#.#.......#.#####.#..#.#..###.#......######....
###........###.....###..#..#....#..#.#....###..#.#....###..#.#..#########......###.#..#.#.#####.....
....##....#...##...###...............#.#.###.....#..#..#..##..#..########..#.#..##..#..#..####......
.##..#...#..#..#..####.#..#.#..#..#..#..####..##..#...##############..#######################.#.#...
###..#....#.#..#.#####..##..#...##..##...##......##....##############.###.##################....#..#
###.......#.#.###.###.##....###......####......###......#############..#..##################..#..#..
###.#..#....#..#..###........................#..............########...#...################..##..#..
####..##..#...#...#######......##.....##...###..............########........################..##...#
.##...##....##....#######...#...#...#..##..###.####......###########.........#############...###..##
.......#####.........####..#..#.#..###..#.####.....##...#############...#...##############...####...
..............#####..####.###.#.#.#####.#..###.##...#...############....#..###############..####....

```

High stop chance, and high width/height to stop partitioning, generate maps with huge continuous cavernous area and small number of sidecaves connected by corridors.

```bash
GENERAL:
            1. Map width: 100
            2. Map height: 50

            BINARY SPACE PARTITIONING:  
            3. Minimum sub area width: 6
            4. Minimum sub_area_height: 6
            5. Stop chance: 50
            6. Width to cease partitioning: 40
            7. Height to cease partitioning: 40

            CELLULAR AUTOMATA:
            8. Initial floor propability: 40
            9. Min neighbouring walls to set wall to floor: 6
            10. Min neighbouring walls to set floor to wall: 3 
            11. Iterations: 3  

            CORRIDORS:
            12. Chance to make a turn on each step: 50
            13. Draw corridors 1 = yes 0 = no: 1

            OUTPUT:
            14. Output to console: 1
            15. Output to file: 1
            16. Path to file: ./data/
            17. Filename: testi.log
            
####################################################################################################
####..............................................######..###..###################..########.#######
###.................................................##...........################......####.......##
########..####...................###.............................#################......###........#
################.................###.......###....................################......###........#
###############...........................####.....................###############......###........#
##############..........................######......................##############.###..###........#
##########......########.#######.......######...##...................############..###..###........#
#..######......########.########.......######...##....................##########........###........#
#...####......#####.##..########..#.............##....................#########.........###........#
#...####......####......######.................................#.......########.........###........#
#....###......###..#...###..............................................######..........###........#
##.......................####............................................####..........####.......##
###......................#######...........##.............................###.........#####.......##
####.....................#######...........##.............................###.........#####......###
#####.....................######...........##...##........#####...........###......##..####....#####
#####.........##...............................####.......######.............####..##...###....#####
######...###..##...............................####.......######......##..#..###........###.#..#####
######...###...................................###..........#####....###.....##........####.#.######
##.##....####......#..........##..............................####..####...............####.#.######
#........########.......#....####.............................##########....#.........#####.#.######
##....#..##..#####..........#####...........................#############.####.........####.#..#####
##............#######....########............###...........####################.........###.##..####
###............#################.......#.....###...........####################.............###..###
###.....#.......#################............####..........####################..........#######..##
###............########....#######............####..........####..############...........########.##
##.............#####..........####....#..##...######.........#.....####.................#########.##
#..............####............###........##...####....#...........###..................#########.##
#...............##....................##......#####......................##......####...#########..#
#....................................####.....####......................####....#####...###...###..#
##...................................####.....####......................####...#######...##........#
##....................................##.......##.......##....##.........##############..##........#
##..###................................................###....###.........#############..##........#
##..###....###.........................................###....###..........#######...#...##........#
##..###....###.........................................####...###.......#.#######...##...##........#
#....##....##..........................................####..............######....####..##........#
#..................######...............#..............####.............######....#####..##........#
#..................#######..............................##....###############......####..###.......#
#.........##.......########.###..............................##############..............###.......#
#........###.......#############...........#.................##############..............###.......#
#........###........############.............................#############.#............####.......#
#####....####........##########.....##....##.................#######.####.......##.....#####......##
######...####..###.....#######.....####...##..#...............#####....#.......####...#####.......##
######...####..###..#...######.....####...##...................####........#...####..######.......##
######....##............######.....###....##..........##........##........###...###########.......##
########...............########...........##.........####.................##.#.....#########......##
#########..............#########..........##........#####..........................#########.......#
##########............##########....................######.........##.......###...############....##
#############.....###############..................#########.....#####...###########################
####################################################################################################


```

Finally settings with low stop chance, small areas, small floor probability, wall to floor and floor to wall theresholds on the large side, one CA iteration and no corridors, generate maps suitable to be used as top down maps of a city. Here the "#" mark the streets and "." buildings.

```bash

GENERAL:
            1. Map width: 100
            2. Map height: 50

            BINARY SPACE PARTITIONING:  
            3. Minimum sub area width: 3
            4. Minimum sub_area_height: 3
            5. Stop chance: 2
            6. Width to cease partitioning: 12
            7. Height to cease partitioning: 12

            CELLULAR AUTOMATA:
            8. Initial floor propability: 6
            9. Min neighbouring walls to set wall to floor: 6
            10. Min neighbouring walls to set floor to wall: 7 
            11. Iterations: 1  

            CORRIDORS:
            12. Chance to make a turn on each step: 50
            13. Draw corridors 1 = yes 0 = no: 0

            OUTPUT:
            14. Output to console: 1
            15. Output to file: 1
            16. Path to file: ./data/
            17. Filename: 2022-03-09.txt
            
#######.############################################################.###############################
#...##..##...##.#.....##..##...#.....##.##.........##.....##...##......#..#.........##..##.........#
#...##..##...##.......##..##.........##.##....#....##....##....##.........##........##..##.........#
#...##..##...##.......##..##..........####.###########.#####....#.........########.###############.#
############################################################...##.........################.#########
############.##########################.##.......####...#.##...##...#.....##...########.##...#.....#
#......##.....#########.....##.......##.##......#####.....##...#######################..##.........#
#......##.....##...##.......##.......##.##.#.....#####################################..##.........#
##.....#......##.####.......##.......##.####.#....###############.......####....#...##..##.##......#
#......##.....##...##.......####.....##.##.#.#.....##...####...##....#..####........##..##..##.....#
###########################.###########.##.........##..#####...##........###........######.........#
##################.###.################.##.........##...####.####..........#......####.###.........#
#.##.......###..##.....##...##.....#.################....####.###....#....##......####..##.........#
#.##.###...##...##.....##...###......################.....##...##...#.....##....######..############
#.###......##...##.....##...###......##..##........##.....##...##.........##.....#####..############
#.######...##...##.....###.........#.##..##........##.##.###################...#######..##..#......#
#.##.......##...##.....#..#.##.......#####....#....###############.#######################.........#
####.......##...##......#.#.##.......######..##.....#.....##.........##.#.################.........#
#####.......#...##.....##...##.......#...############....###.........##...##...######...##.........#
####.......##...##.....##...##.......##..############.....##.....#...##...##.#..####....##.#.......#
####.......##...###....##...##.......##..#......#..##.....#############################.############
###########################.#######.###..##..#.....#......#################################.########
##########.############################..##.###....##.....##........##....#########....##...##...#.#
#.....#.......##.....##.......##.....##..#######...##......#........###...#########....##....##....#
#......#.....###.....##.......##.....#######################.....#..###############....########....#
#.....##......##.....##.......##...#.#######################........########.....##########.###..#.#
###################.################.##......###..##......##........#.....##.....#####.#....###....#
#######################################......##...##......##........##.....#.....#.....##...########
####......####.......##.#...##.......##......##...##......##################.....###...##....#######
####.......###..#....##.....##.......##......##...##......##################...#####...##.#..###...#
#.##........##...#...##...#.##....#####.#.#..#######......##..###...##....##.....##....##....####..#
#.##........##....#..##...#.##...######.#.#.################...#....###...##..#####....##....##.....
#.##........##......###..#####....#############...##########........####..##..####.....#############
#########.#############.##...##......##########...##......##........##..#.##...#####################
####.##################.#...###......#.......##...##......##........##.#..###############.##.#....##
#.##.#####..#....##..##.....###..#.#.##......##...##......##........##....#####.###....##.##........
#.##..####.......######.....####.#.#.##.....########.#######################.....##.....#.##.......#
#####.#################.############.##.#...###########################.#####....###################
####.##############..##.############.##......##......##...##..#....##.....##.....###################
####......#......##..##..##.##.......##......##......##...##.##....##.....##.....##....#.##..##....#
####......##.....###.##..##.##.......##......##......##...##.##....##.....##...#.##.#....##..###...#
#.##......##.....##..##..##.##.......##......##......##...####################.#.###################
#.##......#########..##..##.##.......##....#.##......##...#######.####################.#############
#.##......##.######.############.#######.##############.####.#....##........##.....##.........##...#
#.##....#.##.....##..###################################.###..#...##........##.....##.........##...#
#.##########.....######......##.##...##......##...##.....###......##........###...###.........##...#
#.##########.....######......##.#....##......##.#.##.......#......##........##...####.........##..##
#.##......##.....##..##......##.....###......##...##.#....##......##........##....###.......####..##
#.##......##..#..##..##.....###......##......##...####....###.....##........###..####.....#.####..##
#########################################################################.#######################.##

```

I believe that is enough to show the difference you can make by fiddling with the settings. I still find new combinations that give surprising results, even after spending embarrassingly long time generating maps and marveling my own work. Later on when combined with some game mechanics and added level features, this generator should be able to handle all the levels needed for a fairly complex game.  
