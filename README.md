# Chub.ai Smart Proxy

A smart proxy that fixes Chub.ai's API endpoint issues, updated for DeepSeek v3.1 (via OR) and other reasoning models. Add custom headers, get around CORS headers and get a bit of customization back in.

## Key Notes

- **DeepSeek v3.1 reasoning** - Properly handles thinking/non-thinking requests on Openrouter via header configuration
- **CORS issues** - Bypasses browser CORS restrictions
- **Multiple accounts** - Switch between OpenRouter, DeepSeek direct, OpenAI, etc.
- **Missing headers** - Add customized headers for each provider

## Quick Start (5 minutes)

### 1. Install Requirements

```bash
pip install flask pyyaml requests
```

### 2. Set Up Configuration

```bash
# Copy the example config
cp config.yaml.example config.yaml

# Edit config.yaml and add your API keys
# (or set them as environment variables)
```

### 3. Run the Proxy

```bash
python chub_proxy.py
```

You'll see something like:
```
============================================================
Chub.ai Smart Proxy - v1.1
============================================================

Available Endpoints:
------------------------------------------------------------

  OpenRouter (All Models) (default)
  URL: http://localhost:8080/openrouter
  Status: ✅
```

### 4. Configure Chub.ai

1. In a Chub.ai chat, go to Settings → Secrets → OpenAI
2. Select **"Reverse Proxy"**
3. Paste the chosen endpoint URL into 'OAI Reverse Proxy'
4. Leave API key blank (it's in config.yaml)
5. Click 'Check Proxy' before closing window and refreshing page (F5)

## How It Works

By default, the proxy uses OpenRouter which supports most models including:
- DeepSeek v3.1 (with hybrid reasoning support)
- GPT-5, Claude, Gemini, and 100+ other models
- Some automatic handling of model-specific quirks

Endpoint: `http://localhost:8080/openrouter` (NO trailing slash)

## Configuration Guide

### Basic Setup (config.yaml)

```yaml
profiles:
  openrouter:
    api_key: sk-or-v1-your-key-here  # Your OpenRouter key
```

### Using Environment Variables (More Secure)

```yaml
profiles:
  openrouter:
    api_key: ${OPENROUTER_API_KEY}  # Reads from environment
```

Then set your environment variable:
```bash
# Windows
set OPENROUTER_API_KEY=sk-or-v1-your-key-here

# Mac/Linux
export OPENROUTER_API_KEY=sk-or-v1-your-key-here
```

## Troubleshooting

### "No API key" error
- Make sure you've added your API key to `config.yaml`
- Or set it as an environment variable

### "Connection refused" error in Chub
- Make sure the proxy is running (`python chub_proxy.py`)
- Check the URL is exactly as shown in the proxy output

### Model not thinking when it should
- Custom endpoints: Check your model selection - some APIs require specific models for thinking. If the model doesn't support reasoning, OR will pass the request with the header ignored.
- Models like Nouse Hermes 4 require initiating a `<think>` request.
(Note that Chub.ai strips `<think>` / `</think>` and `reasoning` headers)

### Want to see what's happening?
The proxy logs every request with full details for debugging. Set `debug=False` to disable payload view.
  - `app.run(host='127.0.0.1', port=PROXY_PORT, debug=True)`

## Advanced Features

### Adding Custom Endpoints

The proxy is VERY flexible! Just copy the custom profile example in `config.yaml` and modify:

```yaml
profiles:
  my-custom-api:
    name: My Custom API
    base_url: https://my-api.com/v1
    api_key: ${MY_API_KEY}
    
    # Add as many headers as you need - they all get sent!
    headers:
      X-Custom-Header: my-value
      Another-Header: another-value
      X-API-Version: 2024-01-01
      X-Required-Field: whatever
    
    # Optional: Force a specific model
    force_model: specific-model-name
    
    # Optional: Map model names
    model_map:
      gpt-5: my-model-name  # Rename on the fly
    
    # Optional: Add reasoning parameters (for OpenRouter-compatible endpoints)
    # reasoning:
    #   enabled: true
    #   effort: high    # or medium, low
    #   exclude: false  # true to hide reasoning from output
```

Then access it at: `http://localhost:8080/my-custom-api`
Ensure indentation is proper! YAML configurations are strict with indentation. Follow the provided examples to ensure spacing is correct.

### Supported Features for Custom Endpoints

- **Unlimited headers** - Add as many as your API needs
- **Environment variables** - Use `${VAR_NAME}` for API keys
- **Model forcing** - Always use a specific model
- **Model mapping** - Rename models on the fly
- **Custom base URLs** - Any OpenAI-compatible endpoint
- **Reasoning modes** - Configure how to handle thinking models

## Need Help?

1. Check the proxy console for error messages
2. Make sure your API keys are correct
3. Try the simpler `openrouter` profile first
4. If all else fails, restart everything (Ctrl+C to stop the proxy)
5. If double-else, reach out to Joe. Discord handle: joeh

---

Made for the Chub.ai community by someone who enjoys the hobby and wants others to do the same. Almost all of the work itself was performed by Anthropic Claude.
