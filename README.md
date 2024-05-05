#6.1 Creating a MUD - Phase 1
Overview
Before the dawn of graphical MMORPGs and VR landscapes, there existed a genre of games that allowed players to dive into intricately crafted worlds using the mightiest graphics processor ever: the human imagination. These games were called MUDs - Multi-User Dungeons. 

MUDs are text-based online role-playing games, which can be thought of as precursors to the visually rich online games we see today. In a MUD, players explore virtual worlds, battle monsters, solve puzzles, chat with other players, and gather treasures—all through text commands. Picture reading a fantasy book, but instead of passively following a character's adventure, you're the lead, making choices and shaping the tale

Objective: Develop a simple text-based Multi-User Dungeon (MUD) game in C where a player can navigate through rooms, examine and pickup objects, and view their inventory. By the end, you'll have created this map for your player to explore!

                                    

          North            Temple Altar   

            |              (Healer, Pit)     

            |                    |       

     West---+---East             |     

            |                 Temple of      

            |                   Mota        

          South           (starting point)

                                 |

                                 |   

                  Cleric         | 

       Northern   Guild----Temple Square---Grunting---Bar    Northern

       Midgaard           (Rubber Duck)    Boar Inn          Midgaard

           |     Magic           |                              |

           |     Shop   Bakery   |    Grocer    Weaponsmith     |

           |       |      |      |       |           |          |

      <---West---Main-Street---Market----Main-Street-+--------East--->Cross

          Gate     |      |    Square    |           |        Gate    Roads

           |     Mage  Armoury   |     Jeweler    Warrior       |

           |     Guild           |                 Guild     Wall Road

          Wall                   |                              |

          Road  Leather  Map     |    Grebe's  Pet   Apothecary |

           |     Shop    Shop    |    Tavern   Shop  (Potions)  |

           |      |       |    Common   |       |       |       |

          Wall---Poor-Alley----Square---Dark-Alley------+----Wall Road

          Road    |       |      |      |       |       |       |

           |   Temple   Grubby   |   Thieves  Levee   Warehouse |

           |  Of Stupid  Inn     |    Guild   (Boats) (Sailor)  |

           |                    Dump            |               |

          Wall  Mob              |              |             Wall

          Road--Factory          |              |             Road

           |                   South         Midgaard           |         

         Bridge                Bridge         River---------> Bridge <--> 

           |                     |              |               |         

        Southern              Southern        Kate's          Southern     

        Midgaard              Midgaard        Diner           Midgaard

Map can be found in worldmap.txt 

Where do we start?
Phase 1: Loading the World
Challenge requirements


1. The Makefile

For this challenge and the later portions, the program must use a Makefile, a template is included

The Makefile will build a main.bin and a test.bin from main.c and test.c

The Makefile will compile the .h file data.h into data.o (later stages will have more header files)

The OBJS variable in the Makefile must include the data.o target, which is then used to compile the main.bin and test.bin

The dependencies section of the Makefile (marked by #dependencies) must be updated with the target data.o and the header data.h 

Verify that the Makefile works and creates both the test.bin and  main.bin

coder@/usercode$ make


2.  Data Design

For this challenge, the program must create 2 structs, Room and Object  

The structs must be created in the file data.h

The Room structure that should mimic the JSON's design of a room (e.g., id, name, description…). See example below. The structure can use typedef.

The fields room.name, room. description, object.name, and object description must all be character pointers. For example:

char *name;

Directions, north, south, east, west are integer values that are the room ID of the room in the given direction. For example, if north = 59, then when the player travels north they will be in room 59. All directions should default to -1, which is the same as null in the json file.

The program must implement an Object structure that mimics the json design of an object (e.g., id, name, description). See example below. The structure can use typedef.

Pointers to an array of structs

Various functions will use rooms and objects, which is a pointer to the first element of an array of Room structs or an array of Object structs

struct Room *rooms;

struct Object *objects;

These pointers will most likely be defined in the program's main function

Verify that the program still builds

3. The JSON:

Two files are provided in the template miniworld.json and world.json. 

Do not alter these files, if you accidentally change one, you can get a replacement using the following command from the command line in the /usercode directory

wget https://trickel.com/miniworld.json 
wget https://trickel.com/world.json
No submissions will pass if these files are altered, to check whether you have accidentally altered the files run the follow command, the hexadecimal number should match

coder@/usercode$ sha256sum miniworld.json 
deab1650ebd637ebc1e320d1232e1b8c058e148ac9886eb3efbb8ee44fdcceaf  miniworld.json
coder@/usercode$ sha256sum world.json 
7434bd820668672af68fed91d4080df38c70b154fb682076fb4bec2b8d9877bb  world.json


Filename Argument

The JSON to be used by the program should be defined by an argument when starting the program. For example:

./main.bin miniworld.json
If the argument is not supplied then the program should default to world.json

The program will use the files to load information about the MUD's world, which is limited to rooms and objects.

These are the contents of miniworld.json that is in the template. 

{"rooms":

[

       {

           "id": 1,

           "name": "The Temple Of Mota",

           "description": "You are in the southern end of the temple hall in the Temple of Mota. The temple has been constructed from giant marble blocks, eternal in appearance, and most of the walls are covered by ancient wall paintings picturing gods, giants and peasants. Large steps lead down through the grand temple gate, descending the huge mound upon which the temple is built and ends on the temple square below. Equally large steps lead UP through a small door into the ENTRANCE to MUD SCHOOL.  (type 'up' to go to MUD SCHOOL.)  A small plaque is on this wall.",

           "south": null,

           "north": 4,

           "east": 3,

           "west": null,

           "object": 5,

           "starting": true

       },

    {

       "id": 3,

       "name": "Cleric's Bar",

       "description": "The bar is one of the finest in the land, lucky it is members only.  Fine furniture is set all around the room.  A small sign is hanging on the wall. ",

       "south": null,

       "north": null,

       "east": 1,

       "west": 1,

       "object": 22,

       "starting": false

   },

   {

       "id": 4,

       "name": "Entrance to Cleric's Guild",

       "description": "The entrance hall is a small modest room, reflecting the true nature of the Clerics.  The exit leads east to the temple square.  A small entrance to the bar is in the northern wall. ",

       "south": 1,

       "north": null,

       "east": null,

       "west": null,

       "object": 365,

       "starting": false

   }

],

"objects":

[

       {

       "id": 5,

       "name": "scimitar blade",

       "description": "Hassan's scimitar lies upon the ground, waiting for its owner."

   }, {

       "id": 22,

       "name": "sword long",

       "description": "A long sword has been left here."

   },

       {

       "id": 365,

       "name": "banner war merc",

       "description": "A war banner is on the floor here."

   }

]}





4. Loading the JSON to the structs

The program can not use any external JSON library to read in the file. 

The program must read in and load the contents of the JSON file

The program does not and should not support all JSON file formats, it should only support the format used by these files

Examine the miniworld.json (provided above)

How many values exist per line?

What character by itself on a line starts the list of items (rooms or objects)

What character by itself on a line starts the next item ?

What is the general format of an item's value? 

The value that's always in quotes is called the key and the text after the colon is called the value.

Each value ends with a comma and then a newline ,\n  

What types can a value be?

An integer, which has no quotes but can also hold the value null

A string, which will be in quotes

A boolean, which has no quotes and will be either true or false

Each item's record always ends with which character? The character is usually on a line by itself but not always, sometimes it might appear with the { that starts a new record. Thus, you might code it so that the program uses "id" instead to determine the start of a record.

What character always ends the list of items (rooms and objects)?

Allocating the Heap-Based Arrays

We will store the rooms and objects in their associated heap-based arrays, pointed to by the variables rooms and objects (declared in main, above).

The arrays will be loaded in such a way that a room with an ID of 1, will be in the location 1 of the rooms variable, example

rooms[1] will contain the room with an ID of 1 or the room name "Temple of Mota"

rooms[3] will contain the the room with an ID of 3 aka "Cleric's Bar"

objects[5] will contain the room with an ID of 5 aka scimitar blade

How big do we need the heap-based arrays to be?

There's 3 rooms and 3 objects in miniworld.json, so will objects = malloc(sizeof(Object) * 3) be large enough for loading the objects from miniworld.json

NO! Look back at the IDs in the miniworld.json, what's the largest ID value?

What will happen, if we loaded the object using objects[largest ID value]? Maybe an error maybe not (undefined behavior) accessing the array out of bounds

It seems counting the objects in the JSON file will not work!

So, to know how much memory to allocate for the heap-based array, we need to know the maximum ID

When the IDs will be used for an array index, if the IDs are not contiguous you must allocate space based on the maximum ID

To handle heap allocations for rooms and objects, the program will declare the following functions in data.h and define the functions in data.c

Before programming the functions below, I would encourage you to write your own test for  findMaxId, by updating the function testFindMaxId() in test.c. The test should open a JSON file and pass the opened file to findMaxId and check that the values are as expected

Write the tests for testAllocateObjectAndRoom()

bool allocateObjectAndRoom(char * filename, Room ** roomsPP, Object ** objectsPP)

If unsure why this is a double pointer, refer to slides or lab 

Open the provided filename

Check to make sure the file was opened (hint: this will be a test case, what happens when the filename is non-existant)

use the findMaxId function 

close the file

allocate the memory to the *roomsPP and *objectPP for the heap-based array of structs

Initialize all the values in each Room and each Object to -1, you can use memset (testAllocateObjectAndRoom should test for -1)



memset(*roomsPP, -1, sizeof(Room) * Number_of_Room_in_Array);



return true if successful false if not

void findMaxId(FILE *jsonFile, int * roomMaxId, int * objectMaxId)

Reads the file until it receives the object's closing character (see above)

Does the line contain "\"id\"" 

Extract the room ID value

find the max ID value

Read the file until receiving the object's closing character (see above)

Does the line contain "\"id\"" 

Extract the room ID value

find the max ID value



Define the following functions for loading from the JSON file in the file named data.h and data.c

bool loadJSON(char * filename, Room * rooms, Object * object) 

This function opens the provided file

Checks to make sure it opened the file (hint: this will be a test case, what happens when the filename is non-existant)

Reads until it receives "rooms" or the first opening character 

Loops until it receives the room's closing character

Invoke loadRoom

load the room into rooms (be sure to use the ids)

Reads until receiving "objects" or the object's opening character 

Loops until receiving the closing character 

Invoke loadObject

load the object into objects

return true if successful and false if not

struct Room loadRoom(FILE *jsonFile)

The length of a single line is unknown, so use the getLine(char *line, int * len, FILE * filePtr) function to get the line from the file (see Pig Latin level 5)

Create a local variable using the Room struct

Load the struct's values from the file

return with the struct when the completion character is reached

use each of the field names to find the appropriate field, example: 

if (strstr(line,"\"id\"")){            

    roon.id = extractInt(line);

    

For the direction fields, the value of null is equal to -1
HINT: Do not rely on the "starting" value always being set true for room 1

struct Object loadObject(FILE *jsonFile)

The length of a single line is unknown, so use the getLine(char *line, int * len, FILE * filePtr) function to get the line from the file (see Pig Latin level 5)

Create a local variable using the Room struct

Load the struct's values from the file

return with the struct when the completion character is reached

HINT: start testing with the miniworld.json and then eventually move on to the world.json. 

5. Updating main in the file main.c

Define the local variables for the pointers rooms and objects 

If arg[1] is supplied it should use it for the filename, if it is not supplied it should default the filename to "world.json" 

it should invoke allocateObjectAndRoom if it returns false it should return 88 and exit main

it should invoke loadJSON if it returns false it should return 99

it should print the name's of the rooms, as a comma delimited list (no comma after the last name).

it should print the name's of the objects, as a comma delimited list (no comma after the last name).



6. Testing our Data Loading

As usual, you will be writing several test cases, however, this time we will be writing two types of tests the bash script (black box tests) and the "Unit Tests" using the test.h and test.c files

The Makefile will create the test.bin that will run the suite of unit tests

Many of the tests are stubbed out, each test has a description in the comments above it

There's a total of 8 unit tests in test.c 

To run all of the tests at once, after using make

./test.bin 
7. Model bad versions       

It's not reasonable to give you a modelBad for each of the 8 tests; however, the following are how the modelBad versions on the submission server perform the break

testFindMax - last line of findMax returns -1

testAllocateObjectAndRoom_Rooms - after malloc of roomsPP disable an initialization step that sets all values in roomsPP to -1.

testAllocateObjectAndRoom_Objects - after malloc of objectsPP disable an initialization step that sets all values in objectsPP om to -1.

testLoadRoom - in loadRoom after setting the id, the bad version always reverts it to r.id = 33;

testLoadObject - in loadObject after setting the id, the bad version always reverts it to oi.id = 33;

testLoadJSON_Small - sets every room.id that's loaded to 33

testloadJSON - sets every object.id to 1999.
