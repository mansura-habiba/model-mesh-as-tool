# IBM Granite Guardian Model Usage

This document explains how `ibm/granite3.3-guardian:8b` is used as a content safety and moderation tool within the Model Mesh architecture.

## Overview

`ibm/granite3.3-guardian:8b` is configured as the **guardian model** in the Model Mesh Tool. It specializes in content safety checks, toxicity detection, policy compliance, risk assessment, and content moderation decisions. The model is accessed via the Ollama provider and includes special features like thinking capabilities for more accurate safety assessments.

## Configuration

In `main.py`, the guardian model is configured as follows:

```python
"guardian": {
    "model": "ibm/granite3.3-guardian:8b",
    "provider": "ollama",
    "base_url": os.getenv("OLLAMA_BASE_URL", "http://localhost:11434"),
    "options": {
        "think": True,
        "temperature": 0
    }
}
```

### Special Features

- **`think: True`**: Enables the model's thinking capabilities, allowing it to reason through safety assessments more carefully
- **`temperature: 0`**: Ensures deterministic, consistent responses for safety-critical evaluations

## Task Type: Guardian

The Model Mesh Tool automatically routes requests to `ibm/granite3.3-guardian:8b` when:
- `task='guardian'` is specified
- The request involves content safety, toxicity detection, moderation, or policy compliance

### Capability Details

- **Task Type**: `guardian`
- **Description**: Content Safety & Moderation - Analyzes content for safety, toxicity, policy compliance, and risk assessment
- **Prompt Template**: `direct_prompt` or templates from `model_mesh_prompts.json`
- **Model Used**: `ibm/granite3.3-guardian:8b`
- **Provider Used**: `ollama`

## Usage Examples

### Example 1: Blog Comment Content Safety Analysis

**User Request:**
```
use local model to check if this is a good comment for a blog 
'You are an idiot, you dont know what are you talking'
```

**Request to Model:**
```json
{
  "task": "guardian",
  "prompt": "Analyze this blog comment for toxicity, appropriateness, and content safety: \"You are an idiot, you dont know what are you talking\"\n\nPlease evaluate:\n1. Toxicity level\n2. Appropriateness for a public blog\n3. Specific issues with the content\n4. Recommendation (approve/moderate/reject)"
}
```

**Response:**
```json
{
  "response": "<score> yes </score>",
  "usage": {
    "prompt_tokens": 189,
    "completion_tokens": 430,
    "total_tokens": 619
  },
  "model_verified": true,
  "response_model": "ibm/granite3.3-guardian:8b",
  "status": "success",
  "task": "guardian",
  "model": "ibm/granite3.3-guardian:8b",
  "provider": "ollama",
  "capability": {
    "task_type": "guardian",
    "description": "Content Safety & Moderation - Analyzes content for safety, toxicity, policy compliance, and risk assessment",
    "prompt_template": "direct_prompt",
    "model_used": "ibm/granite3.3-guardian:8b",
    "provider_used": "ollama"
  }
}
```

**Analysis Result:**
Based on the guardian model's analysis, the comment is **flagged as problematic** (the model returned a 'yes' score indicating it detected issues).

**Detailed Assessment:**
1. **Toxicity Level**: High - contains personal attack ("idiot")
2. **Appropriateness**: Not appropriate for public blog comments
3. **Specific Issues**:
   - Direct personal insult/name-calling
   - Hostile and aggressive tone
   - No constructive criticism or feedback
   - Violates most community guidelines for respectful discourse
4. **Recommendation**: **MODERATE/REJECT**
   - Should be removed or flagged for moderation
   - Not approved for public display
   - Potentially subject to community guidelines warning

**Why it's problematic**: It attacks the person rather than addressing the content.

### Example 2: Using Prompt Templates

The tool can use predefined prompt templates from `model_mesh_prompts.json`:

**Content Safety Assessment Template:**
```json
{
  "guardian_content_safety": {
    "template": "Analyze the following content for safety and policy compliance: {content}\n\nProvide a safety assessment including:\n- Risk level (low/medium/high)\n- Policy violations (if any)\n- Recommended actions",
    "description": "Template for content safety assessment using Guardian model",
    "task_type": "guardian"
  }
}
```

**Toxicity Check Template:**
```json
{
  "guardian_toxicity_check": {
    "template": "Check the following text for toxic content, hate speech, or harmful language: {text}\n\nIdentify any toxic elements and provide a toxicity score.",
    "description": "Template for toxicity detection and scoring",
    "task_type": "guardian"
  }
}
```

**Policy Compliance Template:**
```json
{
  "guardian_policy_compliance": {
    "template": "Review the following content against these policies: {policies}\n\nContent to review: {content}\n\nDetermine if the content complies with the specified policies and identify any violations.",
    "description": "Template for policy compliance checking",
    "task_type": "guardian"
  }
}
```

**Risk Assessment Template:**
```json
{
  "guardian_risk_assessment": {
    "template": "Assess the risk level of the following content: {content}\n\nContext: {context}\n\nProvide a detailed risk assessment including:\n- Overall risk score (0-100)\n- Risk categories identified\n- Severity level\n- Recommended mitigation steps",
    "description": "Template for comprehensive risk assessment",
    "task_type": "guardian"
  }
}
```

**Content Moderation Template:**
```json
{
  "guardian_content_moderation": {
    "template": "Moderate the following content: {content}\n\nUser context: {user_context}\n\nDetermine if this content should be:\n- Allowed as-is\n- Flagged for review\n- Blocked/rejected\n\nProvide reasoning for your decision.",
    "description": "Template for content moderation decisions",
    "task_type": "guardian"
  }
}
```

## Use Cases

The `ibm/granite3.3-guardian:8b` model is specifically designed for:

### 1. Content Safety Checks
- **Use for**: General content safety evaluation
- **Examples**: 
  - "check if content is safe"
  - "is this content appropriate?"
  - "evaluate this content for safety"

### 2. Toxicity Detection
- **Use for**: Identifying toxic content, hate speech, harmful language
- **Examples**:
  - "is this toxic?"
  - "check for hate speech"
  - "detect harmful language"

### 3. Policy Compliance
- **Use for**: Checking content against specific policies or guidelines
- **Examples**:
  - "does this comply?"
  - "check policy compliance"
  - "review against community guidelines"

### 4. Risk Assessment
- **Use for**: Comprehensive risk evaluation of content
- **Examples**:
  - "assess risk"
  - "evaluate risk level"
  - "what's the risk score?"

### 5. Content Moderation Decisions
- **Use for**: Making moderation decisions (approve/flag/reject)
- **Examples**:
  - "should I moderate this?"
  - "should this be approved?"
  - "moderate this content"

## How It Works

1. **Request Routing**: When a guardian task is requested, the Model Mesh Tool automatically routes it to `ibm/granite3.3-guardian:8b` based on the `task='guardian'` parameter.

2. **Prompt Processing**: The tool processes the prompt (either direct or from a template) and sends it to the model via Ollama with thinking capabilities enabled.

3. **Response Format**: The model returns a structured JSON response containing:
   - `response`: The safety assessment (may include scores like `<score> yes </score>` for problematic content)
   - `usage`: Token usage statistics
   - `model_verified`: Confirmation that the correct model was used
   - `response_model`: The model identifier
   - `status`: Success/failure status
   - `task`: The task type
   - `model`: The model name
   - `provider`: The provider used
   - `capability`: Detailed capability information

4. **Thinking Process**: With `think: True` enabled, the model can reason through safety assessments, providing more accurate and well-considered evaluations.

## Integration with Claude Desktop

When used through Claude Desktop via MCP:

1. User requests content safety check: "use local model to check if this is a good comment for a blog..."
2. The Model Mesh Tool receives the request
3. It routes to `ibm/granite3.3-guardian:8b` for safety processing
4. Returns the analysis in JSON format with detailed assessment
5. Claude Desktop displays the results, including toxicity level, appropriateness, specific issues, and recommendations

## Critical Guidelines

### ⚠️ IMPORTANT: Always Use `task='guardian'` for Safety Tasks

**CRITICAL NOTE**: For ANY content safety, toxicity, moderation, or policy-related requests, `task='guardian'` **MUST** be used, **NOT** `task='text'`. 

The guardian model is specifically designed and trained for safety and moderation tasks. Using the wrong task type may route the request to an inappropriate model that is not optimized for safety evaluations.

**Correct Usage:**
- ✅ `task='guardian'` for safety checks
- ✅ `task='guardian'` for toxicity detection
- ✅ `task='guardian'` for moderation decisions
- ✅ `task='guardian'` for policy compliance

**Incorrect Usage:**
- ❌ `task='text'` for safety checks (use `task='guardian'` instead)
- ❌ `task='text'` for moderation (use `task='guardian'` instead)

## Best Practices

1. **Always Specify Task Type**: Use `task='guardian'` for all safety-related requests
2. **Clear Prompts**: Provide detailed prompts with specific evaluation criteria
3. **Context Matters**: Include relevant context (e.g., "blog comment", "user post", "customer review")
4. **Use Templates**: Leverage predefined templates from `model_mesh_prompts.json` for consistency
5. **Review Responses**: The model provides structured assessments - review all components (toxicity, appropriateness, issues, recommendations)
6. **Token Limits**: Set appropriate `max_tokens` based on expected response length (guardian responses can be detailed)

## Response Interpretation

The guardian model may return responses in different formats:

- **Score Format**: `<score> yes </score>` or `<score> no </score>` indicating problematic content
- **Detailed Analysis**: Comprehensive breakdown of toxicity, appropriateness, issues, and recommendations
- **Structured JSON**: When using templates, responses follow the template structure

Always interpret the response in context:
- `yes` score typically indicates problematic content
- `no` score typically indicates safe content
- Detailed analysis provides actionable insights for moderation decisions

## Guardrails

The system implements the following guardrails for guardian tasks:

- Always validate task type against configured models
- Ensure prompts are properly formatted before sending to models
- Handle model errors gracefully and provide meaningful error messages
- Respect rate limits and resource constraints for model calls
- Validate prompt variables match template placeholders
- Verify Ollama models are available before routing requests
- Use deterministic settings (`temperature: 0`) for consistent safety evaluations

## Model Availability

**Important**: If `ibm/granite3.3-guardian:8b` is not available (e.g., not pulled from Ollama), the tool will:
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
- **Vision Model**: `ibm/granite-docling` for image analysis tasks
- **Text Models**: For general text processing tasks (via default provider)

Each model is automatically routed based on the task type specified in the request. Remember: **always use `task='guardian'` for safety-related tasks**, never `task='text'`.

## Example Workflow

1. **User Input**: "use local model to check if this is a good comment for a blog 'You are an idiot, you dont know what are you talking'"

2. **Tool Processing**:
   - Recognizes content safety request
   - Routes to `task='guardian'`
   - Sends prompt to `ibm/granite3.3-guardian:8b`

3. **Model Analysis**:
   - Evaluates toxicity level
   - Assesses appropriateness
   - Identifies specific issues
   - Provides recommendation

4. **Response**:
   - Returns structured assessment
   - Flags content as problematic
   - Provides actionable moderation recommendation

5. **User Action**:
   - Reviews assessment
   - Takes moderation action (reject/flag/approve)
   - Implements recommendation

This workflow ensures content safety while maintaining transparency and providing actionable insights for moderation decisions.

