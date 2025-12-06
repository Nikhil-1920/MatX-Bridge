<div align="center">

# 🌉 MatX-Bridge

### *Elegant C++ ↔ MATLAB Integration Made Simple*

[![MATLAB](https://img.shields.io/badge/MATLAB-R2016b+-blue.svg)](https://www.mathworks.com/products/matlab.html)
[![C++11](https://img.shields.io/badge/C++-11-00599C.svg)](https://isocpp.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

*A modern, production-ready template for building MATLAB MEX wrappers around C++ classes with clean OOP semantics*

[Features](#-features) • [Quick Start](#-quick-start) • [Documentation](#-documentation) • [Examples](#-usage-examples) • [Architecture](#-architecture-overview)

---

</div>

## ✨ Features

<table>
<tr>
<td width="50%">

### 🎯 **Developer-Friendly**
- Clean, lowercase naming convention
- Extensively documented code
- Zero dependencies beyond MATLAB
- Modern C++11 patterns

</td>
<td width="50%">

### 🚀 **Production-Ready**
- Robust error handling
- Memory-safe object management
- Multiple instance support
- Thread-safe architecture

</td>
</tr>
<tr>
<td width="50%">

### 📦 **Easy Integration**
- One-command build system
- Drop-in template design
- Minimal boilerplate
- Native MATLAB OOP feel

</td>
<td width="50%">

### 🔧 **Extensible**
- Clear separation of concerns
- Modular architecture
- Easy to adapt and customize
- Well-structured codebase

</td>
</tr>
</table>

---

## 🚀 Quick Start

### Prerequisites

- MATLAB R2016b or newer
- C++ compiler configured with `mex -setup`

### Installation

```matlab
% 1. Clone the repository
git clone https://github.com/yourusername/matx-bridge.git
cd matx-bridge

% 2. Build the MEX interface
buildmatxbridge

% 3. Start using it!
obj = matxbridge(42);
result = obj.compute(2);
disp(result);  % Output: 84
```

**That's it!** You're ready to integrate C++ into your MATLAB workflow.

---

## 💡 Usage Examples

### Basic Object Creation and Method Calls

```matlab
% Create a MatX-Bridge object with preset value
bridge = matxbridge(100);

% Call methods with natural MATLAB syntax
preset = bridge.getpreset();     % Returns: 100
result = bridge.compute(0.5);    % Returns: 50

% Multiple independent instances
obj1 = matxbridge(10);
obj2 = matxbridge(20);

obj1.compute(2)  % Returns: 20
obj2.compute(2)  % Returns: 40
```

### Advanced Integration Pattern

```matlab
% Use in scientific computing pipelines
function processData(inputMatrix)
    % Initialize C++ processor
    processor = matxbridge(length(inputMatrix));
    
    % Process each element with C++ backend
    results = zeros(size(inputMatrix));
    for i = 1:length(inputMatrix)
        results(i) = processor.compute(inputMatrix(i));
    end
    
    % Automatic cleanup when function exits
    clear processor;
end
```

### Integration with MATLAB Classes

```matlab
classdef MyAnalyzer < handle
    properties (Access = private)
        cppEngine
    end
    
    methods
        function obj = MyAnalyzer(config)
            % Embed C++ engine in MATLAB class
            obj.cppEngine = matxbridge(config.preset);
        end
        
        function result = analyze(obj, data)
            result = obj.cppEngine.compute(data);
        end
    end
end
```

---

## 🏗️ Architecture Overview

MatX-Bridge uses a **two-layer wrapper pattern** to provide clean object-oriented semantics:

```
┌─────────────────────────────────────────────────────────────┐
│                     MATLAB Layer                            │
│  ┌───────────────────────────────────────────────────┐     │
│  │  matxbridge.m (MATLAB Class Wrapper)              │     │
│  │  • User-facing OOP interface                      │     │
│  │  • Stores C++ object handle                       │     │
│  │  • Method forwarding                              │     │
│  └────────────────────┬──────────────────────────────┘     │
└─────────────────────────┼──────────────────────────────────┘
                          │ MEX Interface
┌─────────────────────────┼──────────────────────────────────┐
│                     C++ MEX Layer                           │
│  ┌────────────────────┴──────────────────────────────┐     │
│  │  matxbridgemex.cpp (MEX Gateway)                  │     │
│  │  • Command interpreter                            │     │
│  │  • Object lifecycle management                    │     │
│  │  • MATLAB ↔ C++ data conversion                   │     │
│  └────────────────────┬──────────────────────────────┘     │
│                       │                                     │
│  ┌────────────────────┴──────────────────────────────┐     │
│  │  matxbridgecore.cpp (C++ Implementation)          │     │
│  │  • Core business logic                            │     │
│  │  • Pure C++ class                                 │     │
│  │  • No MATLAB dependencies                         │     │
│  └───────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────────┘
```

### Why This Architecture?

**Problem:** MATLAB's MEX interface is function-based, not object-oriented.

**Solution:** MatX-Bridge provides two wrapper layers:

1. **C++ MEX Wrapper** (`matxbridgemex.cpp`)
   - Implements `mexFunction` entrypoint
   - Manages persistent C++ object storage
   - Maps MATLAB handles to C++ instances
   - Dispatches commands to the correct object
   - Handles data type conversions

2. **MATLAB Class Wrapper** (`matxbridge.m`)
   - Provides natural MATLAB OOP syntax
   - Stores handle to underlying C++ object
   - Forwards method calls to MEX layer
   - Ensures clean resource cleanup

**Result:** MATLAB users interact with C++ objects as if they were native MATLAB objects, with full OOP semantics.

---

## 📚 Documentation

### Repository Structure

```
matx-bridge/
├── matxbridgecore.h          # C++ class interface
├── matxbridgecore.cpp         # C++ class implementation
├── matxbridgemex.cpp          # MEX gateway (C++ → MATLAB)
├── matxbridge.m               # MATLAB class wrapper
├── buildmatxbridge.m          # Build script + tests
├── README.md                  # This file
└── LICENSE                    # MIT License
```

### Core Components

#### `matxbridgecore.h` / `matxbridgecore.cpp`
The pure C++ class containing your business logic. This example provides:
- `getpreset()` — Returns the initialization value
- `compute(factor)` — Multiplies preset by a factor

**Key Feature:** Zero MATLAB dependencies — can be tested and developed independently.

#### `matxbridgemex.cpp`
The MEX gateway that bridges MATLAB and C++:
- Creates/destroys C++ objects on demand
- Maintains a registry of active instances
- Interprets MATLAB commands (`new`, `delete`, `getpreset`, `compute`)
- Converts between MATLAB and C++ data types
- Routes calls to the appropriate C++ object

#### `matxbridge.m`
The MATLAB-side wrapper class:
- Provides intuitive OOP interface
- Manages object lifetime with automatic cleanup
- Forwards method calls to MEX layer
- Generates proper MATLAB documentation

#### `buildmatxbridge.m`
Automated build and test script:
- Compiles all C++ sources into MEX binary
- Runs validation tests
- Verifies installation

### Building From Source

```matlab
% Standard build
buildmatxbridge

% After building, view documentation
doc matxbridge

% Run examples
example_usage  % (if provided)
```

### Extending MatX-Bridge

To adapt this template for your own C++ class:

1. **Replace the core C++ class** (`matxbridgecore.*`)
   - Implement your C++ class
   - Keep it independent of MATLAB

2. **Update the MEX gateway** (`matxbridgemex.cpp`)
   - Add command handlers for your methods
   - Update data conversion logic as needed

3. **Update the MATLAB wrapper** (`matxbridge.m`)
   - Mirror your C++ methods in MATLAB
   - Update documentation strings

4. **Rebuild**
   ```matlab
   buildmatxbridge
   ```

---

## 🎯 Use Cases

MatX-Bridge is perfect for:

- **Performance-Critical Algorithms** — Implement computationally intensive code in C++ while maintaining MATLAB workflow
- **Legacy C++ Integration** — Wrap existing C++ libraries for MATLAB users
- **Hardware Interfaces** — Build MATLAB bindings for C++ hardware drivers
- **Cross-Platform Tools** — Share C++ core logic between MATLAB, Python, and standalone applications
- **Commercial Products** — Create professional MATLAB toolboxes with C++ backends

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. 🐛 **Report bugs** — Open an issue with reproduction steps
2. 💡 **Suggest features** — Share your ideas for improvements
3. 📖 **Improve docs** — Help make the documentation clearer
4. 🔧 **Submit PRs** — Fix bugs or add features

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

```
MIT License - Free for commercial and private use
You can use, copy, modify, and distribute this software
with proper attribution.
```

---

## 🌟 Why MatX-Bridge?

| Challenge | MatX-Bridge Solution |
|-----------|---------------------|
| MEX functions aren't object-oriented | Two-layer wrapper provides full OOP semantics |
| Complex boilerplate for object management | Clean, reusable template with all patterns included |
| Difficult to maintain multiple instances | Built-in object registry and lifecycle management |
| Poor MATLAB integration | Native MATLAB class wrapper with natural syntax |
| Hard to understand MEX examples | Extensively commented, production-ready code |

---

## 🙏 Acknowledgments

Built with care for the MATLAB and C++ communities. Special thanks to developers who helped refine this pattern through years of MEX interface evolution.

---

## 📬 Contact & Support

- **Issues:** [GitHub Issues](https://github.com/yourusername/matx-bridge/issues)
- **Discussions:** [GitHub Discussions](https://github.com/yourusername/matx-bridge/discussions)
- **Email:** your.email@example.com

---

<div align="center">

**⭐ If MatX-Bridge helped your project, please star the repository! ⭐**

Made with ❤️ for the MATLAB community

[⬆ Back to Top](#-matx-bridge)

</div>