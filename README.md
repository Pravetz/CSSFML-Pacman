## SFML-Pacman
My relatively old course project, written in C# in MonoDevelop using SFML.Net only, it's state is finished, that's why it's distributed in a single project.zip file, which needs to be extracted and opened in MonoDevelop.

Project has it's own graphics(made in GIMP) and sounds(made in LMMS) inspired by original game from the 80s.

It is very modification-friedly, as all in-game entities, animations and sounds are defined in config files(can be found in `assets` folder in bin/Debug) in home-grown esoteric configuration language, you can read more in `Modification` related sections.

## Modification: Game's filesystem
Game assets such as character appearances, sounds, animations, maps and etc. can be modified without needing to edit game's source code.

Everything is defined in special configuration files in in project's `bin/Debug/assets/configs` folder subdirectories:

  - `animations` contains files describing various animations for ghosts(`ghost.animation`) and Pac-Man(`pacman.animation`)
  - `entities` contains files describing idle textures for ghosts(`ghosts.txt`), ghost eyes(`ghost_eyes_textures.txt`) and Pac-Man(`pacman.txt`)
  - `font` contains bitmap font configurations, which are used by game's localizations to properly draw text in-game
  - `maptiles` contains `maptx.txt` file, which describes texture coordinates of map tiles
  - `sounds` contains descriptions of sounds, which are played when Pac-Man or ghosts do certain actions
  - `UI` contains `paclifes.txt`, which sets texture for Pac-Man lifes "counter" and `ui_overlay.txt`, which contains relative positioning and sizes of text elements in-game

Data for config files is located in `bin/Debug/assets/gfx`(for textures and map layouts) and `bin/Debug/assets/sfx`(for sounds)

In-game text strings are defined in language-specific files, e.g. `EN.txt` for English text or `UA.txt` for Ukrainian.

Game also has `settings.txt` file, which is created automatically on it's first start, settings can not be changed in-game, but the file can be open and changed using Notepad or similar programs.

## Modification: Game Settings
Game uses automatically created settings file to locate and load necessary data, as well as configure some rules, following configurations can be edited:
```
FULLSCREEN: Whether to start in fullscreen mode(True/False, default value is True)
WINDOW_WIDTH: Window width if game doesn't use full screen(integer), default value is 1368
WINDOW_HEIGHT: Window height if game doesn't use full screen(integer), default value is 768
GAME_TX_PATH: Game texture atlas path, default is assets/gfx/game_atlas.png
GHOSTS_DEF_PATH: Ghosts definition file path, default is assets/configs/entities/ghosts.txt
PACMAN_DEF_PATH: Pac-Man defintion file path, default is assets/configs/entities/pacman.txt
GHOST_EYES_TXDEF: Ghost eyes` textures path, default is assets/configs/entities/ghost_eyes_textures.txt
MAP_TILE_TXDEF_PATH: Map tiles definition file path, default is assets/configs/maptiles/maptx.txt
MAPS_PATH: Maps path, default is assets/gfx/maps/
DEFAULT_MAP: Default map name in MAPS_PATH, default is classic_map.png
DEFAULT_LANG: Game localization language, default is EN
CHARS_PATH: Bitmap font definition files path, default is assets/configs/font/
TEXT_PATH: Localization strings path, default is assets/text/
LOG_PATH: Logging path, default is logs/
UI_OVERLAY_PATH: UI overlay definition file path, default is assets/configs/UI/ui_overlay.txt
PACMAN_LIFES_BAR_DEF: Pac-Man life bar texture definition path, default is assets/configs/UI/paclifes.txt
SOUND_VOLUME: Game sound volume, default is 100
RANDOM_MAPS: Whether to load random map from MAPS_PATH each time player wins a level(True/False), default is False
```

## Modification: Editing Animations
Animation files have their own syntax rules, which are fairly simple, to (re-)define animations
Sample code: 
```
"idle" 0,001
	td4 0 0 16 0 16 16 0 16
```

This is "idle" animation for Pac-Man, `"idle"` is animation identifier, `0,001` is time interval between each animation frame, `td4` or `texture direction 4` is the Pac-Man's character direction in game, for which this frame will get applied.
There are 5 directions, which can be used with `td`:

  - `0`: right
  - `1`: up
  - `2`: left
  - `3`: down
  - `4`: any direction, use this to specify animation frames regardless of character's movement direction

`td` must always be followed with 8 integer values, those are (x, y) pairs of texture coordinates on game atlas (`bin/Debug/assets/gfx/game_atlas.png`), to specify second frame in selected texture direction, just write another line starting with same texture direction specifier, e.g.

```
"move" 0,005
	td0 0 0 16 0 16 16 0 16
	td0 16 0 32 0 32 16 16 16
	td0 32 0 48 0 48 16 32 16
	td0 48 0 64 0 64 16 48 16
	td0 64 0 80 0 80 16 64 16
	td0 80 0 96 0 96 16 80 16
	
	td1 0 16 16 16 16 32 0 32
	td1 16 16 32 16 32 32 16 32
	td1 32 16 48 16 48 32 32 32
	td1 48 16 64 16 64 32 48 32
	td1 64 16 80 16 80 32 64 32
	td1 80 16 96 16 96 32 80 32
	
	td3 0 16 16 16 16 32 0 32
	td3 32 32 16 32 16 16 32 16
	td3 48 32 32 32 32 16 48 16
	td3 64 32 48 32 48 16 64 16
	td3 80 32 64 32 64 16 80 16
	td3 96 32 80 32 80 16 96 16
	
	td2 16 0 0 0 0 16 16 16
	td2 32 0 16 0 16 16 32 16
	td2 48 0 32 0 32 16 48 16
	td2 64 0 48 0 48 16 64 16 
	td2 80 0 64 0 64 16 80 16
	td2 96 0 80 0 80 16 96 16
```

In-game characters have fixed amount of animations, new ones can not be added(without changing the code), but can be defined in any order.


## Modification: Editing Entities
Pac-Man and Ghosts are defined in `ghosts.txt` and `pacman.txt` files which use a little different syntax.
For Pac-Man, definition is following:

```r255, g255, b0, a255, 0, 0, 16, 0, 16, 16, 0, 16, x16, y16```

`r<INT>`, `g<INT>`, `b<INT>` and `a<INT>` specify RGBA color for Pac-Man's body, next 8 integer values specify his default texture, just in case idle animation was not defined for some reason, `x<INT>`, `y<INT>` specify Pac-Man's size on map.

Ghosts are defined in single file:

```
"Blinky", r255, g0, b0, a255, 0, 64, 16, 64, 16, 80, 0, 80, x16, y16
"Pinky", r255, g184, b255, a255, 0, 64, 16, 64, 16, 80, 0, 80, x16, y16
"Inky", r0, g255, b255, a255, 0, 64, 16, 64, 16, 80, 0, 80, x16, y16
"Clyde", r255, g184, b81, a255, 0, 64, 16, 64, 16, 80, 0, 80, x16, y16
```

The overall structure resembles Pac-Man's definition file, with addition of character name strings as first argument. Those don't just "name" ghosts, they actually specify their behavior on map, allowing to create new ghost characters with their behavior copying one of the 4 existing ones (Blinky, Pinky, Inky or Clyde), e.g.

```
"Blinky", r82, g7, b37, a255, 0, 64, 16, 64, 16, 80, 0, 80, x16, y16
```

Will create another ghost with dark-pink color and behavior of Blinky

## Modification: Editing Fonts
Fonts are also present on the game's texture atlas(`bin/Debug/assets/gfx/game_atlas.png`), each localization needs it's own bitmap font specified(it can be copied from existing ones, though).

Syntax for bitmap font definition is fairly simple, here's example `EN.txt`:

```
OFFSET 0 120
A 6 8
B 5 8
C 5 8
D 5 8
E 5 8
F 5 8
G 6 8
H 5 8
I 1 8
J 2 8
K 5 8
L 5 8
M 5 8
N 5 8
O 5 8
P 5 8
R 5 8
S 5 8
T 5 8
U 5 8
V 5 8
W 9 8
X 5 8
Y 5 8
Z 6 8
```

OFFSET keyword here signals game's pixel scanner to jump to position (0, 120) on in-game atlas before reading any characters, then, there are tuples of 3 values: character, it's width and height.

Scanner goes horizontally from left-most position specified by OFFSET to right, if your bitmap font is positioned on multiple lines in `game_atlas.png`, don't be afraid to use OFFSET again in same file.

For example, numbers are located lower than latin letters on game atlas, so to read them too, another offset is specified after all the letters are scanned:
```
OFFSET 0 128
0 5 8
1 3 8
2 5 8
3 5 8
4 6 8
5 5 8
6 5 8
7 6 8
8 4 8
9 5 8
```

## Modification: Editing Map Tiles
Map Tiles, or "Cells" are defined in `maptx.txt`, example syntax for wall cells:
```
CELLTYPE WALL
0 80 16 80 16 96 0 96
16 80 32 80 32 96 16 96
32 80 48 80 48 96 32 96
160 80 176 80 176 96 160 96
48 80 64 80 64 96 48 96
80 80 96 80 96 96 80 96
112 80 128 80 128 96 112 96
224 80 240 80 240 96 224 96
64 80 80 80 80 96 64 96
96 80 112 80 112 96 96 96
128 80 144 80 144 96 128 96
208 80 224 80 224 96 208 96
144 80 160 80 160 96 144 96
176 80 192 80 192 96 176 96
192 80 208 80 208 96 192 96
256 80 272 80 272 96 256 96
```

To begin defining cell textures, one writes CELLTYPE followed by cell name in uppercase, then, in new lines, all possible textures are defined as 8 integer values(x,y coordinate pairs) for the cell depending on it's direction and possible intersections with other cells.

For edibles, each new entry specifies new fruit which will appear on map depending on level

## Modification: Editing Sounds
Sounds are attached for certain actions characters do, those actions are hardcoded, but as with animations, sounds can be defined for them in any order, example for Pac-Man:

```
"eat"=assets/sfx/pacman/eat.ogg
"eat_fruit"=assets/sfx/pacman/eat_fruit.ogg
"eat_ghost"=assets/sfx/pacman/eat_ghost.ogg
"energizer"=assets/sfx/pacman/energized.ogg
"death"=assets/sfx/pacman/death.ogg
```

First goes action identifier, then, separated by = sign, path to .ogg sound file.

In-game sound player doesn't play same sound for all characters, only unique ones, to prevent "earraping" the player.

## Modification: Editing and Creating Localizations
By default, game is supplied with English and Ukrainian localizations, but user can create his own localizations and put them in `bin/Debug/assets/text` with unique file name.

As a reference, one should take English localization file and then translate each line to target language:

```
PACMAN
<ENTER> - PLAY
<ESC> - EXIT
- BLINKY (CHASER)
- PINKY (AMBUSHER)
- INKY (FICKLE)
- CLYDE (FEIGNED IGNORANCE)
- PACMAN
READY!
SCORE: 
GAME OVER!
<ENTER> - RESTART
<ESC> - EXIT
GAME PAUSED.
```

One should also create or copy already existing bitmap font definition for a language as described in `Editing Fonts` section.

## Modification: Editing UI Overlay
User can also edit the positioning, color and scaling of in-game text using `ui_overlay.txt` file.
It is composed of lines with 4 or 8 floating point and integer values:
```
0,076 0,02297 4 4 255 255 0 255
0,076 0,425 1 1
0,076 0,450 1 1
0,099 0,16 1,5 1,5 255 0 0 255
0,099 0,192 1,5 1,5 255 184 255 255
0,099 0,224 1,5 1,5 0 255 255 255
0,099 0,256 1,5 1,5 255 184 81 255
0,099 0,288 1,5 1,5 255 255 0 255
0,089 0,1 1,5 1,5 255 255 0 255
0 -0,022125 2 2
0,45 0,45 2 2 255 0 0 255
0,452 0,66 1 1
0,452 0,685 1 1
0,073 -0,02 1,5 1,5 255 255 0 255
```

Two first floating point values specify text position in relation to game's window size, next two floating point values specify text scaling(widht, height), this should be 1 1 by default.

This data is sufficient for game to draw text properly, but if you want to get fancy, you can also add coloring for text by specifying extra 4 integer values, RGBA values in strict order, first goes Red, then Green, then Blue and then Alpha value.

Each ui overlay entry corresponds to localization strings in files found in `bin/Debug/assets/text`, so, if you are feeling lost, you can lookup the text you need to adjust overlay for there.

```
0,076 0,02297 4 4 255 255 0 255 (in ui_overlay.txt) --> PACMAN (in bin/Debug/assets/text/EN.txt)
```

## Modification: Editing and Adding Maps
Game can load any number of maps defined in `bin/Debug/assets/gfx/maps` folder as long as they follow the pattern:

  - Each pixel is a separate cell
  - Each pixel color determines separate cell type
  - There are NO undefined cell types

Cell types are following:
```
  Wall - RGBA(33,33,222,255)
  Ghost house door - RGBA(159,133,115,0)
  Edible(default) - RGBA(255,255,255,0)
  Edible(energizer) - RGBA(255,255,255,1)
  Edible(fruit) - RGBA(255,255,255,2)
  Empty - RGBA(0,0,0,0)
  Pacman's initial position - RGBA color defined in `bin/Debug/assets/configs/entities/pacman.txt`
  Ghosts' initial positions - RGBA colors defined in `bin/Debug/assets/configs/entities/ghosts.txt`
```

Any other color values are considered undefined and will lead to game's crash when loading map.
