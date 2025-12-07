# Model Mesh as Tool

Model Mesh Composer for Claude Desktop that sets up a Model Mesh Tool with Guardian, Vision, and Text models for use with Claude Desktop via MCP (Model Context Protocol).

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

4. Restart Claude Desktop to load the new MCP server

## Configuration

The tool is configured with:
- **Guardian model**: `ibm/granite3.3-guardian:8b` with thinking capabilities
- **Vision model**: `ibm/granite-docling` for document processing
- **Base URL**: Configurable via `OLLAMA_BASE_URL` environment variable (default: `http://localhost:11434`)

You can customize the configuration by modifying the `model_config` dictionary in `main.py`.

## Notes

- If some models are not available (e.g., not pulled from Ollama), the tool will:
  - Show warnings during initialization
  - Continue to work with available models
  - Return graceful error messages when unavailable models are called
- This allows the tool to work even if not all models are installed

