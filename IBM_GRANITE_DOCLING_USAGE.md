# IBM Granite Docling Vision Tool Usage

This document explains how `ibm/granite-docling` is used as a vision analysis tool within the Model Mesh architecture.

## Overview

`ibm/granite-docling` is configured as the **vision model** in the Model Mesh Tool. It specializes in image analysis, object detection, and visual content understanding. The model is accessed via the Ollama provider and automatically routes vision-related tasks.

## Configuration

In `main.py`, the vision model is configured as follows:

```python
"vision": {
    "model": "ibm/granite-docling",
    "provider": "ollama",
    "base_url": os.getenv("OLLAMA_BASE_URL", "http://localhost:11434")
}
```

## Task Type: Vision

The Model Mesh Tool automatically routes requests to `ibm/granite-docling` when:
- `task='vision'` is specified
- The request involves image analysis, object detection, or visual content understanding

### Capability Details

- **Task Type**: `vision`
- **Description**: Image Analysis - Analyzes images for objects, scenes, and visual content
- **Prompt Template**: `direct_prompt`
- **Model Used**: `ibm/granite-docling`
- **Provider Used**: `ollama`

## Usage Examples

### Example 1: Basic Image Analysis

**Request:**
```json
{
  "task": "vision",
  "prompt": "Analyze this image in detail. Describe what you see, including: 1. The main subject of the image 2. Colors, lighting, and composition 3. Background and setting 4. Any notable details or features 5. The overall mood or style of the photograph",
  "max_tokens": 1000
}
```

**Response:**
```json
{
  "response": "The image is taken during a sunset shot where there are many bright lights in the foreground while having some areas with more muted colors. There's also an important detail that indicates the presence of people around, and along with them, there are some elements that indicate that it's not all by chance or a random event but rather something else that is present within this photo frame.",
  "usage": {
    "prompt_tokens": 65,
    "completion_tokens": 75,
    "total_tokens": 140
  },
  "model_verified": true,
  "response_model": "ibm/granite-docling",
  "status": "success",
  "task": "vision",
  "model": "ibm/granite-docling",
  "provider": "ollama"
}
```

### Example 2: Using Prompt Templates

The tool can use predefined prompt templates from `model_mesh_prompts.json`:

**Vision Analysis Template:**
```json
{
  "vision_analysis": {
    "template": "Analyze the following image and describe what you see: {image_description}. Focus on: {focus_areas}",
    "description": "Template for vision analysis tasks",
    "task_type": "vision"
  }
}
```

**Vision Object Detection Template:**
```json
{
  "vision_object_detection": {
    "template": "Identify and list all objects in this image: {image_description}. Provide bounding box coordinates if possible.",
    "description": "Template for object detection in images",
    "task_type": "vision"
  }
}
```

## How It Works

1. **Request Routing**: When a vision task is requested, the Model Mesh Tool automatically routes it to `ibm/granite-docling` based on the `task='vision'` parameter.

2. **Prompt Processing**: The tool processes the prompt (either direct or from a template) and sends it to the model via Ollama.

3. **Response Format**: The model returns a structured JSON response containing:
   - `response`: The analysis text
   - `usage`: Token usage statistics
   - `model_verified`: Confirmation that the correct model was used
   - `response_model`: The model identifier
   - `status`: Success/failure status
   - `task`: The task type
   - `model`: The model name
   - `provider`: The provider used

## Integration with Claude Desktop

When used through Claude Desktop via MCP:

1. User requests image analysis: "analyze this image using local tool"
2. The Model Mesh Tool receives the request
3. It routes to `ibm/granite-docling` for vision processing
4. Returns the analysis in JSON format
5. Claude Desktop displays the results

## Use Cases

The `ibm/granite-docling` vision tool is ideal for:

- **Image Analysis**: Detailed descriptions of image content, composition, and style
- **Object Detection**: Identifying and listing objects within images
- **Scene Understanding**: Analyzing scenes, backgrounds, and settings
- **Visual Content Understanding**: Interpreting visual elements, colors, lighting, and mood
- **Document Processing**: Analyzing document images and extracting visual information

## Best Practices

1. **Specify Task Type**: Always use `task='vision'` for image-related requests
2. **Clear Prompts**: Provide detailed prompts for better analysis results
3. **Token Limits**: Set appropriate `max_tokens` based on expected response length
4. **Error Handling**: The tool gracefully handles cases where the model is unavailable

## Guardrails

The system implements the following guardrails for vision tasks:

- Always validate task type against configured models
- Ensure prompts are properly formatted before sending to models
- Handle model errors gracefully and provide meaningful error messages
- Respect rate limits and resource constraints for model calls
- Verify Ollama models are available before routing requests

## Model Availability

**Important**: If `ibm/granite-docling` is not available (e.g., not pulled from Ollama), the tool will:
- Show warnings during initialization
- Continue to work with available models
- Return graceful error messages when unavailable models are called

This allows the tool to work even if not all models are installed.

## Environment Configuration

The model uses the `OLLAMA_BASE_URL` environment variable (default: `http://localhost:11434`). To use a different Ollama instance:

```bash
export OLLAMA_BASE_URL=http://your-ollama-server:11434
```

## Related Models

The Model Mesh Tool also includes:
- **Guardian Model**: `ibm/granite3.3-guardian:8b` for content safety and moderation
- **Text Models**: For text processing tasks (via default provider)

Each model is automatically routed based on the task type specified in the request.

