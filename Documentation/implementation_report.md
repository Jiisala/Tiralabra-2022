# Implemention Report

## General structure

The program launches with launch.py which boots up the UI. The UI it self is a crude but sufficient text UI. It allows user to manipulate various setings and atributes for the map creation and output maps. All of the parameters and settings are stored in params.py file. Main engine is in the logic.py file.

When the main engine is initiated, it loads current parameters and prepares an map in the dimensions specified in parameters. The map is a 2D array with 1 to indicate walls and 0 for floor. When initiated it will be just full of ones. When UI calls for creation of a map it calls various algorithms, each in residing in its own class, to work together and create the map. It firs calls the BSP class to create a tree of rect entities. Rects have four points in x/y axels and up to two smaller child rect entities. It then calls BSP class again this time to create a list of leaf nodes of the tree. The leaf nodes are used to create a small parts of the map. For each of them the program calls cellular automata and then flood fill to fill in possible blanks. The program then stiches the map together and depending of the settings, carves tunnels between all of the nodes, outputs the map to terminal and/or outputs it to a txt file (with current settings included)

## BSP (Binary Space Partitioning)

Coming soon

## Cellular automata

Coming soon

## Floodfill

Coming soon

##Corridors

Allso coming soon