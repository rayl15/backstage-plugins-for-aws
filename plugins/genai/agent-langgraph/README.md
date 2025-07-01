# Generative AI plugin for Backstage - LangGraph Agent Type

This package implements an agent for the Generative AI plugin for Backstage based on [LangGraph.js](https://github.com/langchain-ai/langgraphjs).

Features:

1. [ReAct pattern](https://react-lm.github.io/) to use available tools to answer prompts
1. Choose between Amazon Bedrock, OpenAI, Azure OpenAI, or Google Gemini as the model provider
1. Optionally use the Backstage sqlite/Postgres database as a checkpoint store
1. Integrate with [LangFuse](https://github.com/langfuse/langfuse) for observability

## Configuration

This agent can be configured at two different levels, global and per-agent

### Global

Global configuration values apply to all agents, all of this is optional:

```yaml
genai:
  langgraph:
    memory: # (Optional) Memory store to use
    recursionLimit: # (Optional) Limit the number of graph supersteps (default 25)
    langfuse: # (Optional) Configuration for LangFuse observability
      baseUrl: http://localhost:3001 # (Required) LangFuse URL
      publicKey: pk-aaa # (Required) Public key
      secretKey: sk-bbb # (Required) Secret key
```

The available options for `memory` are:

- `in-memory`: (Default) Store the agent state in memory
- `backstage`: Uses the Backstage database to store agent state, either sqlite or PostgresQL depending on the configuration

## Tested Models

The following models have been tested and verified to work:

### AWS Bedrock
- `anthropic.claude-3-5-sonnet-20241022-v2:0`
- `anthropic.claude-3-haiku-20240307-v1:0`

### OpenAI
- `gpt-4`
- `gpt-3.5-turbo`

### Azure OpenAI
- `gpt-4` (deployment)
- Note: `o1-mini` requires API version `2024-12-01-preview` and uses `max_completion_tokens`

### Google Gemini
- `gemini-1.5-pro`
- `gemini-1.5-flash` (recommended for free tier - higher rate limits)

### Per-agent

Per-agent configuration only applies to the agent for which it corresponds. The available parameters are:

```yaml
genai:
  agents:
    general:
      description: [...]
      prompt: [...]
      langgraph:
        messagesMaxTokens: 100000 # (Required) Prune message history to maximum of this number of tokens
        temperature: 0 # (Optional) Model temperature
        maxTokens: 4000 # (Optional) Maximum output tokens
        topP: 0.9 # (Optional) Model topP
        # Only include the subsequent section for your model provider
        # Bedrock only
        bedrock:
          modelId: 'anthropic.claude-3-5-sonnet-20241022-v2:0' # (Required) Bedrock model ID
          region: us-west-2 # (Required) Bedrock AWS region
        # OpenAI only
        openai:
          apiKey: ${OPENAI_API_KEY} # (Required) OpenAI API key
          modelName: 'gpt-4' # (Optional) OpenAI model name
          baseUrl: ${OPENAI_API_BASE_URL} # (Optional) URL for OpenAI API endpoint
        # Azure OpenAI only
        azureOpenai:
          apiKey: ${AZURE_OPENAI_API_KEY} # (Required) Azure OpenAI API key
          endpoint: ${AZURE_OPENAI_ENDPOINT} # (Required) Azure OpenAI endpoint URL
          deploymentName: ${AZURE_OPENAI_DEPLOYMENT_NAME} # (Required) Azure deployment name
          apiVersion: '2024-02-01' # (Optional) Azure OpenAI API version
        # Google Gemini only
        gemini:
          apiKey: ${GOOGLE_AI_API_KEY} # (Required) Google AI API key
          modelName: 'gemini-1.5-pro' # (Optional) Gemini model name
```
