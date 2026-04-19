# 🔷 Flowchart Designer & Simulator

A C++ object-oriented application that allows users to **design**, **simulate**, and **save** flowcharts through a graphical interface.

---

## 📌 Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Main Classes](#main-classes)
- [Supported Statement Types](#supported-statement-types)
- [Application Modes](#application-modes)
- [File Format](#file-format)
- [Extra Features](#extra-features)
- [How to Run](#how-to-run)

---

## 📖 Overview

This application is a **flowchart designer and simulator** built in C++ using Object-Oriented Programming. Users can:
- Graphically draw flowcharts using various statement blocks
- Connect statements with directional arrows
- Execute (simulate) the flowchart and view output
- Save and load flowcharts from text files

---

## ✨ Features

### Design Mode
| Operation | Description |
|---|---|
| Add Statement | Add Start, End, Declare, Assign, Condition, Read, Write blocks |
| Add Connector | Connect two statements with a directional arrow |
| Select / Unselect | Click to select or deselect a statement or connector |
| Edit Statement | Modify the text/content of a selected statement |
| Delete | Remove a selected statement (and its connectors) or a connector |
| Copy | Copy a selected statement to clipboard |
| Cut | Cut a selected statement (removes it and its connectors) |
| Paste | Paste the clipboard statement at a new location |
| Save Flowchart | Save the current flowchart to a text file |
| Load Flowchart | Load and redraw a saved flowchart from a text file |

### Simulation Mode
| Operation | Description |
|---|---|
| Validate | Check if the flowchart is logically correct and complete |
| Run | Execute the flowchart and display output of Write statements |

---

## 🗂 Project Structure

```
FlowchartDesigner/
├── ApplicationManager.h / .cpp   # Maestro class – manages all components
├── Input.h / .cpp                # Handles ALL user input through GUI
├── Output.h / .cpp               # Handles ALL GUI output (drawing, toolbars, messages)
├── Connector.h / .cpp            # Connector between two statements
├── HelperFn.h / .cpp             # Utility functions (IsValue, IsVariable, etc.)
├── Defs.h                        # Enumerations and constants
├── UI_Info.h                     # GUI layout configuration
├── Statements/
│   ├── Statement.h / .cpp        # Abstract base class for all statements
│   ├── StartStatement.h / .cpp
│   ├── EndStatement.h / .cpp
│   ├── DeclareStatement.h / .cpp
│   ├── ValueAssign.h / .cpp
│   ├── VarAssign.h / .cpp
│   ├── OpAssign.h / .cpp
│   ├── ConditionalStatement.h / .cpp
│   ├── ReadStatement.h / .cpp
│   └── WriteStatement.h / .cpp
├── Actions/
│   ├── Action.h / .cpp           # Abstract base class for all actions
│   ├── AddValueAssign.h / .cpp
│   ├── AddConnector.h / .cpp
│   ├── SelectAction.h / .cpp
│   ├── EditStatement.h / .cpp
│   ├── DeleteAction.h / .cpp
│   ├── CopyAction.h / .cpp
│   ├── CutAction.h / .cpp
│   ├── PasteAction.h / .cpp
│   ├── SaveAction.h / .cpp
│   ├── LoadAction.h / .cpp
│   ├── ValidateAction.h / .cpp
│   └── RunAction.h / .cpp
└── Resources/                    # Icon images (.jpg)
```

---

## 🏗 Main Classes

### `Input`
Handles **all user inputs** through the GUI (mouse clicks, keyboard). No other class reads input directly. Key functions:
- `GetUserAction()` – Detects which action the user triggered
- `GetValue()` – Reads a valid `double` value
- `GetVariable()` – Reads a valid variable name
- `GetArithOperator()` – Reads `+`, `-`, `*`, or `/`
- `GetCompOperator()` – Reads `==`, `!=`, `<`, `<=`, `>`, or `>=`

### `Output`
Responsible for **all GUI output**: drawing shapes, toolbars, status bar messages, and the output bar. Key functions:
- `DrawDesignToolBar()` / `DrawSimulationToolBar()` – Draws the toolbars
- `DrawAssign()`, `DrawCondition()`, `DrawRead()`, etc. – Draws each statement type
- `DrawConnector()` – Draws a connector arrow
- `PrintMessage()` – Displays messages in the status bar

### `ApplicationManager`
The **maestro class** — orchestrates everything without performing other classes' logic.
- Maintains `StatList` (array of `Statement*`) and `ConnList` (array of `Connector*`)
- Only class with direct access to these lists
- Calls `UpdateInterface()` to redraw everything after each action

### `Statement` (Abstract Base)
Base class for all statement types. Key virtual functions:
- `Draw(Output*)` – Draw the statement on screen
- `Save(ofstream&)` – Save statement data to file
- `Load(ifstream&)` – Load statement data from file
- `GenerateCode(ofstream&)` *(Bonus)* – Write equivalent C++ code

### `Action` (Abstract Base)
Base class for all operations. Key virtual functions:
- `Execute()` – Reads parameters then performs the action
- `ReadActionParameters()` – Gets needed input from the user

---

## 🧱 Supported Statement Types

| Shape | Type | Description |
|---|---|---|
| Oval | Start / End | Entry and exit points |
| Rectangle | Declare | Declare and initialize a `double` variable |
| Rectangle | Value Assign | `X = 5.0` |
| Rectangle | Variable Assign | `X = Y` |
| Rectangle | Operator Assign | `X = Y + Z`, `A = A - 1` (supports `+`, `-`, `*`, `/`) |
| Diamond | Conditional | `if` / `while` condition; supports `==`, `!=`, `<`, `<=`, `>`, `>=` |
| Parallelogram | Read | Read variable from keyboard |
| Parallelogram | Write | Write variable value or string message to output |

> **Note:** Only `double` variables are supported.

---

## 🖥 Application Modes

### Design Mode (default)
The user builds the flowchart graphically. The toolbar contains icons for all design operations.

### Simulation Mode
The user validates and runs the flowchart:
- **Validate** checks: exactly one Start & End, correct connections, correct connector counts per statement type, variables declared before use, and more.
- **Run** executes the chart, tracking variable values and displaying Write output. Prompts the user for input on Read statements.

The app window is divided into:
- **Tool Bar** – Action icons for the current mode
- **Drawing Area** – Where the flowchart is drawn
- **Status Bar** – Messages and error descriptions
- **Output Bar** – Simulation output (visible in both modes)

---

## 💾 File Format

Flowcharts are saved as plain text files with the following structure:

```
<Number of Statements>
<Type> <ID> <X> <Y> [additional fields...]
...
<Number of Connectors>
<SourceID> <DestID> <Branch>
...
```

**Branch values:** `0` = regular, `1` = YES branch (conditional), `2` = NO branch (conditional)

**Example:**
```
5
STRT 1 200 90
DECLARE 2 200 150 price
READ 3 150 250 price
COND 4 200 280 price GRT 10000
END 5 200 450
4
1 2 0
2 3 0
3 4 0
4 5 1
```

> Statement IDs must be unique but do not need to be consecutive.

---

## Extra Features

- [ ] **Debug Mode** – Step-by-step execution showing variable values at each step
- [ ] **Generate C++ Code** – Exports an equivalent `.cpp` file from the flowchart

---

## ▶ How to Run

1. Clone the repository
   ```bash
   git clone https://github.com/<your-username>/<repo-name>.git
   ```
2. Open the project in **Visual Studio**
3. Make sure all resource images (`.jpg` icons) are in the correct path defined in `UI_Info.h`
4. Build and run the project (`F5`)

> **Note:** All input and output is handled through the GUI window — no console I/O.

---
