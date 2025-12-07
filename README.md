# Model Mesh as Tool

Model Mesh Composer is an MCP server which exposes different small LLMs such as IBM Granite Guardian, IBM Granite Docling, and other models via MCP (Model Context Protocol).

## Prerequisites

- Python 3.12 or higher
- [uv](https://github.com/astral-sh/uv) package manager
- Ollama running locally (default: `http://localhost:11434`)
- Required Ollama models:
  - `ibm/granite3.3-guardian:8b` (Guardian model)
  - `ibm/granite-docling` (Vision model)

## Installation

1. Clone or navigate to this repository:
   ```bash
   cd model-mesh-as-tool
   ```

2. Install dependencies using `uv sync`:
   ```bash
   uv sync
   ```

   This will create a virtual environment and install all required dependencies.

## Usage

1. Make the script executable:
   ```bash
   chmod +x main.py
   ```

2. Configure in Claude Desktop config file:
   - Add this tool to your Claude Desktop MCP configuration
   - The script runs in stdio mode for Claude Desktop integration

3. Run the script:
   ```bash
   uv run main.py
   ```

   Or activate the virtual environment and run:
   ```bash
   source .venv/bin/activate  # On macOS/Linux
   python main.py
   ```

## Configure with Claude Desktop 

Here is the Claude configuration 

```
"model-mesh": {
      "command": "/absolute path to/model-mesh-as-tool/.venv/bin/python",
      "args": [
        "/absolute path to/model-mesh-as-tool/main.py"
      ],
      "env": {
        "OLLAMA_BASE_URL": "http://localhost:11434"
      }
    }
```

## Configuration

The tool is configured with:
- **Guardian model**: `ibm/granite3.3-guardian:8b` with thinking capabilities
- **Vision model**: `ibm/granite-docling` for document processing
- **Base URL**: Configurable via `OLLAMA_BASE_URL` environment variable (default: `http://localhost:11434`)

To add more model make sure they are avail;able through ollama and then update the configuration input for `ModelMeshTool`

You can customize the configuration by modifying the `model_config` dictionary in `main.py`.

## Notes

- If some models are not available (e.g., not pulled from Ollama), the tool will:
  - Show warnings during initialization
  - Continue to work with available models
  - Return graceful error messages when unavailable models are called
- This allows the tool to work even if not all models are installed

