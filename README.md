# **Section 1: Packet Tracer Interpreter for ACL Configuration – Machine Project Overview**

This project implements a **Cisco Packet Tracer-inspired network command interpreter** that applies core principles of **compiler design**. It demonstrates how **lexical analysis, tokenization, syntax analysis, and command execution** can be applied to networking commands, bridging programming language concepts with real-world networking.

# **Section 2: Description of the Input Language**

The input language designed for this interpreter is a **simplified command-line interface (CLI)** inspired by real-world network configuration tools such as Cisco IOS. It allows users to build, configure, and visualize a simulated network topology through buttons with descriptive labels and text fields for identifying IP addresses and the like, offering an intuitive way to model routers, switches, hosts, and servers. This language bridges the gap between complex networking syntax and an educational, Python-based simulation environment.

## **2.1 Structure**
The interpreter recognizes a predefined set of command tokens, grouped into two main categories:
| **Category** | **Example Tokens / Commands** | **Purpose** |
|---------------|-------------------------------|--------------|
| **Network Topology** | `add router <name>`, `add switch <name>`, `connect <dev1> <dev2> <cable>`, `finish` | Creates and connects devices, finalizes topology setup. |
| **CLI Configuration** | `enable`, `configure terminal`, `set hostname <dev> <name>`, `set interface <dev> <iface> <ip> <mask>` | Simulates router CLI commands for configuration. |
| **Routing Protocols** | `set ospf <dev> <process> <network> <wildcard> <area>`, `set eigrp <dev> <asn> <network>`, `set rip <dev> <network>` | Enables and configures routing protocols. |
| **Access Control Lists** | `set acl-std <dev> <num> <permit/deny> <src>`, `set acl-ext <dev> <num> <permit/deny> <proto> <src> <dst>` | Adds standard or extended ACL rules. |
| **Utility Commands** | `save <dev>`, `show interfaces <dev>`, `show routing <dev>`, `clear cli` | Saves, displays, or resets configurations. |

## **2.2 Example Inputs**
| **Type** | **Command Example** | **Interpreter Response** |
|-----------|--------------------|---------------------------|
| Valid | `add router R1` | Adds router `R1` to the topology. |
| Valid | `connect R1 SW1 ethernet` | Connects `R1` to `SW1` with an ethernet link. |
| Invalid | `addrouter R1` | **LexicalError:** Unrecognized command pattern. |
| Invalid | `set interface R1 g0/0 192.168.1.1` | **SyntaxError:** Missing argument(s). Expected 4, got 3. |

# **Section 3: System Design**
| Library / Module | Purpose / Functionality | Type |
|------------------|------------------------|-------|
| `re` | Used for defining and matching command patterns using **regular expressions** during lexical analysis. | Built-in |
| `os` | Handles basic **file system operations** if needed (e.g., saving configurations). | Built-in |
| `logging` | Provides a flexible framework for emitting log messages from Python programs. Used for application **diagnostics, debugging, and monitoring**. | Built-in |
| `sys` | Captures command-line output and manages **interpreter I/O** streams. | Built-in |
| `io` | Works with in-memory text streams for CLI command capturing. | Built-in |
| `time` | Adds **delays or animations** for simulations and visual feedback. | Built-in |
| `matplotlib.pyplot` | Used for **visualizing the network topology** with device icons and connections. | Third-party |
| `matplotlib.image` | Loads **Cisco device icons** for inclusion in topology diagrams. | Third-party |
| `matplotlib.offsetbox` | Positions image icons precisely within the topology visualization. | Third-party |
| `networkx` | Creates and manages **graph-based representations** of the network topology. | Third-party |
| `ipywidgets` | Provides **interactive buttons and UI controls** for executing commands or visualizing updates in Colab. | Third-party |
| `IPython.display` | Displays widgets, plots, and formatted output directly in Jupyter/Colab cells. | Third-party |

**Importing necessary libraries:**
```
import re                             # Regular Expressions
import os                             # File system operations
import logging                        # Logging
import matplotlib.pyplot as plt       # Plotting
import networkx as nx                 # Graph/network structures
import matplotlib.image as mpimg      # Read/Load Cisco device icon images
from matplotlib.offsetbox import OffsetImage, AnnotationBbox  # Position icons in plots
import ipywidgets as widgets          # Interactive widgets for creating buttons, sliders, and other UI controls
import time                           # Time delays for button flashing or simulations
from IPython.display import display   # Display widgets, outputs, and plots
import io, sys                        # Utility: run one interpreter sentence and capture printed output; return (printer_output, cli_history_text, cli_commands_text)
import gradio as gr                   # Import gradio for User Interface
```

# **Section 4: Data Preprocessing and Cleaning**
The interpreter architecture follows the classic three-stage compiler model, composed of the Lexer (Tokenizer), Parser, and Executor (Interpreter Engine). Input commands first pass through the Lexer, which uses regular expressions to identify command patterns and extract arguments, converting raw text into structured tokens. The Parser then validates these tokens through syntax and semantic analysis, ensuring the correct command format, valid arguments, and logical consistency using the shared symbol_table. Once validated, the Executor performs the corresponding action, such as adding devices, connecting nodes, or generating CLI commands. Errors encountered at any stage trigger specific handlers—LexicalError, SyntaxError, or SemanticError—to provide user-friendly feedback without halting execution. This modular, bottom-up design was chosen for clarity, scalability, and to closely mirror how real interpreters process and execute language commands, making it both educational and practical for network simulation.

**Architecture Overview:**
| Component | Main Functions/Variables | Purpose |
|-----------|--------------------------|---------|
| `Lexer` | `TOKENS`, `tokenize()` | Converts user input into tokens using regex patterns, identifying commands and extracting arguments. |
| `Parser` | `syntax_check()`, `semantic_check()`, `EXPECTED_ARGS` | Validates command syntax (structure) and semantics (logic), ensuring valid arguments and device existence. |
| `Executor` | `exec_add_device()`, `exec_connect()`, `append_cli_block()`, `command handlers` | Executes validated commands, updates the `symbol_table`, and generates CLI configurations or topology changes. |
| `Handler Registry` | `@command_handler`, `handlers{} dictionary` | Maps command tokens to execution functions using decorator pattern for modular command handling. |
| `Symbol Table` | `symbol_table (devices, connections, cli_commands)` | Stores the current network state — including devices, connections, and CLI command history. |
| `Error Handling` | `LexicalError`, `SyntaxError`, `SemanticError` | Captures and displays clear feedback when invalid commands, structures, or logical inconsistencies occur. |
| `Interpreter` | `handle_command()`, `run_interpreter_capture()` | Orchestrates the complete compiler pipeline (Lexer → Parser → Executor) and manages command execution flow. |
| `Logging System` | `ColorFormatter`, `logger` | Logging system that tracks interpreter activity per stage. Timestamps and levels (INFO, WARNING, ERROR, CRITICAL) are used to visualize compiler flow and runtime diagnostics. |
