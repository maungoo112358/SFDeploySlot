# SFDeploySlot

A Go-based CLI tool for automating hot deployment of Java game extensions to SmartFox Server 2X.

## Overview

SFDeploySlot streamlines the development workflow for SmartFox Server 2X game extensions by automating the entire deployment pipeline:

1. **Build** - Compiles Java source files and packages them into a JAR
2. **Deploy** - Copies the JAR and configuration files to the SmartFox extensions directory
3. **Restart** - Gracefully restarts the SmartFox server
4. **Cleanup** - Removes temporary build artifacts

## Features

- Automatic Java 11 detection (JAVA_HOME, PATH, common installation directories)
- SmartFox Server directory validation
- Classpath auto-configuration from SmartFox lib directory
- Process management for SmartFox server (port 9933)
- JSON configuration file deployment
- Detailed console output with progress indicators

## Requirements

- **Go 1.21.6+** (for building from source)
- **Java 11** (JDK required for `javac` and `jar` commands)
- **SmartFox Server 2X** installed
- **Windows** (primary platform; Linux/macOS support is partial)

## Installation

### From Source

```bash
git clone https://github.com/yourusername/SFDeploySlot.git
cd SFDeploySlot
go build -o SFDeploySlot
```

### Pre-built Binary

Download the latest release from the [Releases](https://github.com/yourusername/SFDeploySlot/releases) page.

## Configuration

Create or edit `sfdeploy_config.json` in the same directory as the executable:

```json
{
  "java_path": "C:\\Program Files\\Java\\jdk-11\\bin",
  "source_dir": "C:\\Projects\\MyGame\\GameExtension",
  "target_dir": "C:\\SmartFoxServer_2X",
  "extension_folder": "MyExtension",
  "extension_file": "GameExtension.jar",
  "json_source_dir": "C:\\Projects\\MyGame\\GameExtension\\src\\config\\jsons",
  "deploy_json_files": ["GameConfig", "LevelData", "PlayerSettings"]
}
```

### Configuration Fields

| Field | Description |
|-------|-------------|
| `java_path` | Path to Java 11 `bin` directory (optional if Java is in PATH) |
| `source_dir` | Root directory of your Java game extension project |
| `target_dir` | SmartFox Server 2X installation directory |
| `extension_folder` | Name of the extension folder within SmartFox `extensions` directory |
| `extension_file` | Output JAR filename |
| `json_source_dir` | Directory containing JSON configuration files to deploy |
| `deploy_json_files` | List of JSON filenames (without `.json` extension) to copy |

## Usage

Run the executable from the command line:

```bash
./SFDeploySlot
```

The tool will execute the following phases:

```
Phase 1: Directory Setup
  - Validates configuration file
  - Checks source and target directories
  - Verifies Java 11 installation

Phase 2: Building Project
  - Cleans old .class files
  - Compiles all Java source files
  - Creates JAR file

Phase 3: Deploying Project
  - Terminates processes on port 9933
  - Copies JAR to SmartFox extensions folder
  - Deploys JSON configuration files

Phase 4: Restarting SmartFox Server
  - Launches SmartFox server with logging

Phase 5: Cleaning Up
  - Removes compiled .class files
  - Deletes temporary JAR from source directory
```

## Project Structure

```
SFDeploySlot/
├── main.go              # Entry point and workflow orchestration
├── config.go            # Configuration loading and validation
├── build.go             # Java compilation and JAR creation
├── deploy.go            # File deployment and cleanup
├── server.go            # SmartFox server management
├── utils.go             # Utility functions (Java detection, prompts)
├── sfdeploy_config.json # Configuration file
└── go.mod               # Go module definition
```

## Source Directory Requirements

Your Java project source directory must have:
- A `src/` subdirectory containing `.java` files
- Standard Java package structure

Example:
```
GameExtension/
├── src/
│   └── com/
│       └── mycompany/
│           └── game/
│               ├── MainExtension.java
│               └── handlers/
│                   └── LoginHandler.java
└── lib/                  # Optional external dependencies
```

## SmartFox Server Requirements

The tool validates the target directory contains:
- `SFS2X/` directory
- `sfs2x.bat` launcher script
- `lib/` directory (used for classpath construction)

## Troubleshooting

### Java Not Found
The tool searches for Java 11 in:
1. `JAVA_HOME` environment variable
2. System PATH
3. Common Windows paths:
   - `C:\Program Files\Eclipse Adoptium\jdk-11*`
   - `C:\Program Files\Java\jdk-11*`
   - `C:\Program Files\OpenJDK\jdk-11*`

If not found, you'll be prompted to enter the path manually.

### Port 9933 Already in Use
The tool automatically terminates processes using port 9933 before deployment. If this fails, manually stop SmartFox Server before running the tool.

### Compilation Errors
Check the console output for `javac` error messages. Common issues:
- Missing dependencies in SmartFox `lib/` directory
- Syntax errors in Java source files
- Incompatible Java version

## License

[Add your license here]

## Contributing

[Add contribution guidelines here]
