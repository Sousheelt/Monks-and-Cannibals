# Monks-and-Cannibals
A classic river crossing puzzle game ("Monks and Cannibals") implemented in the Jack programming language for the Nand2Tetris Hack computer platform.

## 📖 About

This is an implementation of the famous "Missionaries and Cannibals" puzzle, adapted as "Monks and Cannibals" for the Hack computer platform. The game challenges players to transport all monks and cannibals across a river using a boat, while following specific rules to ensure the monks are never outnumbered by cannibals on either bank.

### Historical Context
The Missionaries and Cannibals problem is a classic river-crossing puzzle that has been used in AI and algorithm courses for decades. It's often used to teach:
- State space search algorithms
- Graph traversal techniques
- Constraint satisfaction problems
- Game theory fundamentals

This implementation brings the puzzle to life on the Hack computer, providing an interactive experience with real-time graphics, timing challenges, and a competitive leaderboard system.

## 🎮 Game Rules

### Objective
Transport all monks and cannibals from the left bank to the right bank of the river safely.

### Core Constraints
1. **Boat Capacity**: The boat can hold a maximum of 2 people at a time
2. **Boat Operation**: At least 1 person must be in the boat for it to move
3. **Safety Rule**: Monks can never be outnumbered by cannibals on either bank
   - If cannibals > monks on any bank (and monks > 0), the monks get eaten and you lose!
   - Equal numbers are safe (e.g., 2 monks and 2 cannibals is OK)
   - Having only cannibals on a bank is safe (no monks to eat)
4. **Boat Travel**: The boat must physically move between banks to transport people

### Time Limit
Complete the puzzle within **5 minutes** (300 seconds). The timer counts down in real-time and is displayed on screen.

### Difficulty Levels
- **Easy (Level 1)**: 3 monks + 3 cannibals (6 people total)
  - Optimal solution: 11 moves
  - Good for learning the mechanics
  
- **Medium (Level 2)**: 4 monks + 4 cannibals (8 people total)
  - More complex planning required
  - Multiple valid solution paths
  
- **Hard (Level 3)**: 5 monks + 5 cannibals (10 people total)
  - Requires careful strategic planning
  - Significantly more moves needed

### Win Conditions
- All monks successfully transported to right bank
- All cannibals successfully transported to right bank
- No monks were eaten during the process
- Completed within the 5-minute time limit

### Lose Conditions
- Monks outnumbered by cannibals on either bank
- Time runs out (5 minutes elapsed)

## 🎯 Controls

### Keyboard Commands

| Key | Action | Description |
|-----|--------|-------------|
| **M** | Monk | Add or remove a monk from the boat (depending on current state) |
| **C** | Cannibal | Add or remove a cannibal from the boat (depending on current state) |
| **Space** | Move Boat | Move the boat to the opposite bank (requires at least 1 person) |
| **Q** | Quit | Exit the game immediately |
| **1/2/3** | Difficulty | Select difficulty level during game setup |

### Control Behavior
- **Adding to Boat**: Press M or C when the person is on the same bank as the boat
  - Only works if there's space in the boat (max 2 people)
  - Automatically removes the person from the bank
  
- **Removing from Boat**: Press M or C when that type of person is already in the boat
  - Returns the person to the current bank (left or right)
  - Frees up space in the boat

- **Moving the Boat**: Press Space when at least one person is in the boat
  - Boat travels to the opposite bank
  - All people in the boat move with it
  - Game checks for violations after each move

## 🏗️ Project Structure

The project consists of four main Jack source files:

### Main.jack
The entry point of the program. Initializes and runs the game.

### Game.jack
Core game logic and state management (approximately 350 lines):
- **State Variables**: 
  - `mLeft`, `cLeft`: Monks and cannibals on left bank
  - `mRight`, `cRight`: Monks and cannibals on right bank
  - `mBoat`, `cBoat`: Monks and cannibals in boat
  - `boatOnLeft`: Boolean for boat position
  - `gameOver`, `gameWon`: Game state flags
  - `moveCount`: Tracks number of moves
  - `startTime`: Countdown timer (starts at 300 seconds)
  - `maxPeople`: Varies by difficulty level

- **Key Methods**:
  - `initializeGame()`: Sets up initial game state based on difficulty
  - `chooseDifficulty()`: Interactive difficulty selection (1-3)
  - `handleMonkKeyPress()`: Adds/removes monks from boat
  - `handleCannibalKeyPress()`: Adds/removes cannibals from boat
  - `moveBoat()`: Transfers boat between banks if occupied
  - `checkGameStatus()`: Validates game rules and win/lose conditions
  - `run()`: Main game loop with keyboard polling and timer updates

- **Game Loop Logic**:
  - Polls keyboard every 50ms for responsive controls
  - Updates timer every 1 second (20 ticks × 50ms)
  - Checks for game violations after each action
  - Automatically ends game on win/lose/timeout

### GameUI.jack
User interface and graphics rendering (approximately 400 lines):
- **Screen Layout**:
  - Top 110 pixels: Text area (instructions, status, timer)
  - Bottom 146 pixels: Graphics area (banks, river, boat, people)
  - Screen dimensions: 512×256 pixels (Hack computer standard)

- **Visual Elements**:
  - **Banks**: 110 pixels wide solid rectangles on left and right
  - **River**: 292 pixels wide empty space in the middle
  - **Boat**: 70×25 pixel rectangle that moves between banks
  - **Monks**: Circles (8px radius) with cross symbols inside
  - **Cannibals**: Circles (8px radius) with face features (eyes and mouth)

- **Key Methods**:
  - `drawInitialState()`: Clears screen and draws static elements
  - `drawBackground()`: Renders banks and river
  - `update()`: Refreshes all dynamic elements (called after each action)
  - `clearGameObjects()`: Erases people/boat without destroying background
  - `drawGameObjects()`: Redraws all people on banks and in boat
  - `drawMonk(x, y)`: Renders a monk at specified coordinates
  - `drawCannibal(x, y)`: Renders a cannibal at specified coordinates
  - `updateStatusInfo()`: Updates text displays (timer, moves, counts)
  - `showGameOverMessage(won)`: Displays win/lose message with statistics
  - `showLeaderboard()`: Renders top 5 scores

- **Positioning Logic**:
  - People on banks arranged in rows (monks on bottom, cannibals on top)
  - 22-pixel spacing between people on banks
  - 30-pixel spacing between people in boat
  - Dynamic positioning based on boat location (left or right bank)

### Leaderboard.jack
Score tracking and ranking system (approximately 120 lines):
- **Data Structure**:
  - Four parallel arrays of size 5 (max entries)
  - `names[]`: Array of String objects (player names)
  - `moves[]`: Array of integers (move counts)
  - `times[]`: Array of integers (elapsed time in seconds)
  - `difficulty[]`: Array of integers (1=Easy, 2=Medium, 3=Hard)

- **Key Methods**:
  - `new()`: Constructs leaderboard with empty arrays
  - `addScore()`: Inserts new score in sorted position
  - `getSize()`: Returns current number of entries (0-5)
  - `getName(index)`: Retrieves player name at position
  - `getMoves(index)`: Retrieves move count at position
  - `getTime(index)`: Retrieves completion time at position
  - `getDifficulty(index)`: Retrieves difficulty level at position
  - `dispose()`: Frees memory for all arrays

- **Sorting Algorithm**:
  - Insertion sort based on move count (ascending order)
  - When new score is added, finds correct position
  - Shifts existing entries down to make room
  - If leaderboard is full (5 entries), worst score is dropped
  - Fewer moves = better ranking (primary criterion)

- **Session Persistence**:
  - Leaderboard persists only during current program execution
  - Resets when program is restarted
  - No file I/O (Jack/Hack platform limitation)

## 🚀 Running the Game

### Prerequisites
- Nand2Tetris software suite
- Jack compiler
- Hack VM Emulator

### Compilation and Execution

1. **Compile the Jack files**:
   ```bash
   JackCompiler.bat "c:\Users\anees\Desktop\Projects\Monks vs Cannibals"
   ```
   This will generate `.vm` files for each `.jack` file.

2. **Run on the VM Emulator**:
   - Open the VM Emulator
   - Load the project directory
   - Set the animation speed (Animate: Fast recommended)
   - Click "Run" to start the game

3. **Gameplay**:
   - Enter your name when prompted
   - Select difficulty level (1, 2, or 3)
   - Use M, C, and Space keys to solve the puzzle
   - Try to minimize moves and complete within the time limit!

## 📊 Features

- **Three Difficulty Levels**: Varying numbers of monks and cannibals
- **Timer System**: 5-minute countdown with real-time display
- **Move Counter**: Tracks efficiency
- **Visual Representation**: 
  - Monks displayed with cross symbols
  - Cannibals displayed with facial features
  - Dynamic boat positioning
- **Leaderboard**: Top 5 scores saved per session
- **Game State Validation**: Prevents invalid moves
- **Win/Lose Detection**: 
  - Win: All transported safely
  - Lose: Monks outnumbered or time expired

## 🎨 Screen Layout

```
┌─────────────────────────────────────────┐
│ Instructions & Title                    │
│ Player Info, Moves, Timer               │
│ Bank Status & Boat Contents             │
├─────────────────────────────────────────┤
│                                         │
│    [Monks & Cannibals on Left Bank]     │
│                                         │
│  ████████         [BOAT]        ████████│
│  LEFT                              RIGHT│
│  BANK     ═══════════════════      BANK │
│  ████████                       ████████│
│                                         │
│    [Monks & Cannibals on Right Bank]    │
│                                         │
└─────────────────────────────────────────┘
```

## 🏆 Scoring

Players are ranked by:
1. **Fewest moves** (primary criterion)
2. **Fastest time** (secondary criterion)
3. **Difficulty level** (displayed for context)

## 💡 Strategy Tips

### General Strategy
- **Plan Ahead**: Think 2-3 moves in advance, especially considering return trips
- **Return Trip Problem**: Remember someone must bring the boat back!
- **Safe States**: Always leave banks in safe configurations before moving
- **Minimize Waste**: Try to maximize boat usage (2 people per trip when possible)

### Optimal Solutions
- **Easy (3 pairs)**: 11 moves is optimal
  - Example opening: Send 2 cannibals, return 1 cannibal
- **Medium (4 pairs)**: More complex, multiple solutions exist
- **Hard (5 pairs)**: Requires sophisticated planning

### Common Mistakes to Avoid
1. **Forgetting Return Trips**: Don't strand the boat on one side
2. **Unsafe States**: Carefully check monk/cannibal ratios before pressing Space
3. **Rushed Moves**: Take your time - 5 minutes is plenty for careful planning
4. **Overfilling Boat**: Maximum 2 people - the game prevents this but plan accordingly

### Advanced Techniques
- **Cannibal Shuttling**: Use cannibals to ferry the boat back safely
- **Controlled Groups**: Move monks in protected groups when possible
- **End Game Planning**: The last few moves are critical - plan the sequence
- **Time Management**: Aim for under 3 minutes to leave room for mistakes

## 🛠️ Technical Details

### Platform Specifications
- **Target Platform**: Hack Computer (Nand2Tetris course project)
- **Language**: Jack programming language
- **Compilation**: Jack → VM code → Hack assembly
- **Execution**: Hack VM Emulator or Hack CPU Emulator

### Hardware Constraints
- **Screen Resolution**: 512×256 pixels (monochrome bitmap)
- **Screen Memory**: 8192 16-bit words (16KB)
- **RAM**: 16K words of general-purpose RAM
- **Keyboard**: Memory-mapped keyboard register
- **No Native Graphics**: All graphics drawn pixel-by-pixel

### Memory Management
- **Manual Allocation**: `Array.new()`, `String.new()` require explicit sizing
- **Manual Deallocation**: `dispose()` must be called to prevent memory leaks
- **Object References**: Jack uses reference semantics (pointers)
- **No Garbage Collection**: Developer responsible for all memory cleanup

### Input System
- **Polling-Based**: `Keyboard.keyPressed()` returns current key code
- **Blocking Input**: `Keyboard.readChar()` waits for key press
- **No Event System**: Game loop continuously polls keyboard
- **Key Codes**: ASCII values (Space=32, M=77, C=67, Q=81, etc.)

### Graphics System
- **Screen API**: 
  - `Screen.drawPixel(x, y)`: Draw single pixel
  - `Screen.drawLine(x1, y1, x2, y2)`: Bresenham's line algorithm
  - `Screen.drawRectangle(x1, y1, x2, y2)`: Filled rectangle
  - `Screen.drawCircle(x, y, r)`: Midpoint circle algorithm
  - `Screen.setColor(bool)`: true=black, false=white
  - `Screen.clearScreen()`: Erase entire screen

- **Text Output API**:
  - `Output.moveCursor(row, col)`: Position text cursor (23 rows × 64 columns)
  - `Output.printString(str)`: Print text at cursor
  - `Output.printInt(int)`: Convert and print integer
  - Character size: 8×11 pixels

### Performance Considerations
- **Game Loop**: 50ms delay per iteration (20 FPS)
- **Timer Update**: Every 1 second (20 loop iterations)
- **UI Refresh**: On-demand after actions (not every frame)
- **Wait Delays**: 
  - 200ms after user action (prevents accidental double-input)
  - 2000ms for game over message
  - 5000ms for leaderboard display

### Code Organization
- **Total Lines**: ~1,000 lines of Jack code across 4 files
- **Classes**: 4 (Main, Game, GameUI, Leaderboard)
- **Methods**: ~40 total across all classes
- **Fields**: ~25 instance variables for game state
- **Design Pattern**: Model-View separation (Game = model, GameUI = view)

## 📝 Code Structure

The codebase follows object-oriented principles:
- **Encapsulation**: Game state hidden within classes
- **Separation of Concerns**: Logic (Game) separated from UI (GameUI)
- **Modular Design**: Each class has a single, clear responsibility
- **Accessor Methods**: Public interfaces for reading game state

## 🐛 Known Limitations

### Platform Limitations
- **No Persistent Storage**: Leaderboard resets when program ends (Jack has no file I/O)
- **Fixed Resolution**: 512×256 pixels only (Hack hardware constraint)
- **Monochrome Display**: Black and white only (no grayscale or color)
- **Limited Memory**: Must fit within 16KB RAM constraints
- **No Sound**: Hack platform has no audio capabilities

### Game Design Limitations
- **Session-Only Leaderboard**: Scores lost when program exits
- **Fixed Difficulty Levels**: Only 3 predefined levels (3, 4, or 5 pairs)
- **Single Player**: No multiplayer or network functionality
- **No Undo**: Cannot reverse moves (by design for challenge)
- **No Hints**: No built-in hint system for stuck players

### Technical Limitations
- **Performance**: Game loop runs at ~20 FPS (50ms delay)
- **Input Lag**: 200ms delay after actions prevents rapid inputs
- **Graphics Quality**: Simple geometric shapes due to pixel-level drawing
- **Animation**: No smooth transitions (instant state changes)
- **Text Display**: Limited to 23 rows × 64 columns

### Potential Improvements (Future Work)
- Save leaderboard to memory locations that persist
- Add animation for boat movement
- Implement hint system showing valid next moves
- Add sound effects using Hack speaker simulation
- Create level editor for custom puzzles
- Implement undo/redo functionality
- Add move history display

## 📚 Learning Value

### Programming Concepts Demonstrated

#### 1. Game Development Fundamentals
- **Game Loop Pattern**: Continuous update-render-input cycle
- **State Management**: Tracking game state across multiple variables
- **Input Handling**: Keyboard polling and action dispatching
- **Timer Implementation**: Countdown timer with real-time updates
- **Win/Lose Conditions**: Complex boolean logic for game rules

#### 2. Object-Oriented Programming
- **Encapsulation**: Private fields with public accessor methods
- **Composition**: Game class contains GameUI and Leaderboard objects
- **Single Responsibility**: Each class has one clear purpose
- **Constructor/Destructor**: `new()` and `dispose()` patterns
- **Method Design**: Cohesive methods with clear inputs/outputs

#### 3. Graphics Programming
- **2D Rendering**: Drawing primitives (circles, rectangles, lines)
- **Coordinate Systems**: Working with x,y pixel coordinates
- **Double Buffering Simulation**: Clear-then-draw pattern
- **Sprite Positioning**: Dynamic object placement
- **Text Rendering**: Combining graphics and text displays

#### 4. Data Structures & Algorithms
- **Arrays**: Parallel arrays for leaderboard data
- **Insertion Sort**: Sorting scores by move count
- **State Machine**: Game state transitions (playing → game over → leaderboard)
- **Validation Logic**: Rule checking with multiple conditions

#### 5. Low-Level Programming
- **Memory Management**: Manual allocation and deallocation
- **Bit Manipulation**: Working with memory-mapped I/O
- **Performance Optimization**: Minimizing redraws and computations
- **Resource Constraints**: Working within 16KB RAM limit

#### 6. Software Engineering
- **Code Organization**: Logical file structure
- **Naming Conventions**: Descriptive variable and method names
- **Code Documentation**: Inline comments explaining logic
- **Error Prevention**: Validation before state changes

### Nand2Tetris Curriculum Alignment

This project is ideal for students who have completed:
- **Project 9**: High-level programming (Jack language)
- **Project 10-11**: Compiler construction (understanding compilation)
- **Project 7-8**: Virtual machine implementation (understanding VM code)

### Skills Developed
- Translating puzzle logic into code
- Managing complex interactive state
- Creating visual feedback systems
- Implementing competitive scoring
- Debugging with limited tools
- Thinking within platform constraints
- Creating complete user experiences

## 👨‍💻 About Jack Language

Jack is a simple, object-oriented programming language used in the Nand2Tetris course. It was designed for educational purposes to teach compiler construction and high-level programming on a minimalist platform.

### Language Features

#### Supported
- **Classes**: Object-oriented programming with class definitions
- **Methods**: Functions, constructors, and member methods
- **Data Types**: int (16-bit), boolean, char, objects, arrays
- **Fields**: Instance variables for object state
- **Local Variables**: Method-scoped variables
- **Control Structures**: if, if-else, while loops
- **Operators**: +, -, *, /, &, |, ~, <, >, =
- **Standard Library**: Math, String, Array, Output, Screen, Keyboard, Memory, Sys

#### Not Supported
- **No Inheritance**: Classes cannot extend other classes
- **No Interfaces**: No abstract types
- **No Polymorphism**: No method overriding
- **No For Loops**: Must use while loops
- **No Switch/Case**: Must use if-else chains
- **No Floating Point**: Only 16-bit integers
- **No Pointers**: References only (similar to Java)
- **No Structs**: Must use classes

### Compilation Process
```
Jack Source Code (.jack)
    ↓
Jack Compiler
    ↓
VM Code (.vm)
    ↓
VM Translator
    ↓
Hack Assembly (.asm)
    ↓
Assembler
    ↓
Machine Code (.hack)
```

### Jack Syntax Example
```jack
class Example {
    field int value;
    
    constructor Example new(int initialValue) {
        let value = initialValue;
        return this;
    }
    
    method int getValue() {
        return value;
    }
    
    method void setValue(int newValue) {
        let value = newValue;
        return;
    }
    
    method void dispose() {
        do Memory.deAlloc(this);
        return;
    }
}
```

### Key Differences from Java/C++
- **Let Statement**: Variable assignment uses `let x = 5;`
- **Void Returns**: Even void methods need explicit `return;`
- **This**: Must explicitly return `this` from constructors
- **Disposal**: Must manually call `dispose()` to free memory
- **Arrays**: Created with `Array.new(size)`, not `new int[size]`
- **Strings**: Mutable objects, not immutable primitives

## 🎓 Nand2Tetris Context

### Course Overview
Nand2Tetris (officially "From Nand to Tetris: Building a Modern Computer From First Principles") is a comprehensive course that teaches computer science from the ground up. Students build an entire computer system from basic logic gates to high-level applications.

### Course Progression
1. **Projects 1-5**: Build hardware (ALU, RAM, CPU, computer)
2. **Projects 6**: Write an assembler for Hack assembly language
3. **Projects 7-8**: Build a VM translator (VM code → Assembly)
4. **Project 9**: High-level programming in Jack (THIS PROJECT)
5. **Projects 10-11**: Build a Jack compiler (Jack → VM code)
6. **Project 12**: Implement Jack operating system

### Where This Project Fits
This game is a **Project 9** implementation, which focuses on:
- Gaining experience with Jack programming
- Understanding high-level language abstractions
- Preparing for compiler construction in Projects 10-11
- Demonstrating that the Hack platform can run real applications

### Learning Outcomes
By studying or recreating this project, students will:
- Master Jack language syntax and semantics
- Understand object-oriented design without inheritance
- Learn to work within severe platform constraints
- Practice manual memory management
- Experience full-stack development (from logic gates to GUI)
- Appreciate the abstractions modern languages provide

### Prerequisites for This Project
Students should understand:
- Boolean logic and binary arithmetic (Projects 1-2)
- Computer architecture (Projects 3-5)
- Assembly language programming (Project 6)
- Virtual machine concepts (Projects 7-8)
- Jack language basics (Project 9 introduction)

### Pedagogical Value
This project demonstrates:
- That complex, interactive programs can run on minimal hardware
- The power of layered abstractions (gates → assembly → VM → Jack)
- How game logic maps to code structure
- The importance of careful state management
- Real-world application of theoretical concepts

## 📄 License

This is an educational project created for the Nand2Tetris platform.

---

**Enjoy the game and happy puzzle solving! 🧩**
