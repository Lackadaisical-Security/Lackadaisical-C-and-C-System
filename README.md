# Lackadaisical Command and Control Security System

A comprehensive network monitoring and command system for overseeing connected systems in a corporate environment.

## Overview
This system provides centralized monitoring and control capabilities for enterprise networks, with components written in assembly, C, C++, Python, and JavaScript, prioritizing performance and security.

## Components
- **Core**: Low-level components written in Assembly and C
- **Agent**: Client-side software for monitored systems (C/C++)
- **Server**: Command center for agent management (C++/Python)
- **Web Interface**: Administration dashboard (Python/JavaScript)

## Languages Used (in preference order)
1. Assembly - For critical, performance-sensitive operations
2. C - For core functionality and agent implementation
3. C++ - For server components and plugins
4. Python - For API, database interfaces, and web backend
5. JavaScript - For web frontend

## Setup
See the installation guide in `scripts/install.sh` for detailed setup instructions.

## Security Notice
This software is intended for legitimate network monitoring and administration purposes only.

## Implementation Status

### Completed Components:
- Windows x64 shellcode for C2 agent injection
  - Socket creation and connection to C2 server (192.168.1.100:12345)
  - Dynamic function resolution for Windows APIs
  - Command execution capabilities
  - Basic command protocol implementation

### Supported Commands:
- Command 0x01: Execute shell commands using cmd.exe
- Command 0x02: Basic shellcode injection capability
- Command 0x03: Keylogger functionality stub

### Security Features:
- Minimal network footprint
- Non-persistent design for stealth operation
- Dynamic API resolution

### Future Improvements:
- Implement encrypted communications
- Add persistence mechanisms
- Expand available commands
- Implement full data exfiltration capabilities
- Develop additional anti-analysis features
