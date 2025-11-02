# LiteMAAS Reference (Stub)

Use LiteMAAS as the LLM backend for chat completions. Configure these variables in `.env`:

- `LITEMAAS_BASE_URL`
- `LITEMAAS_API_KEY`

## Example Request (pseudo)
```bash
curl -sS -H "Authorization: Bearer $LITEMAAS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "model": "llama-4-scout-17b",
        "messages": [{"role":"user","content":"Hello!"}]
      }' \
  "$LITEMAAS_BASE_URL/v1/chat/completions"
```

Adjust paths and fields to your environment. Document any findings in your team `README.md`.

## Production patterns (minimal)

### Error handling
- Set client timeouts to 10–15s for LLM calls
- Retry on 429 (rate limit) with exponential backoff (e.g., 250ms → 1s → 2s)
- Log errors with a request ID (do not log secrets)

### Security
- Never log or expose `LITEMAAS_API_KEY`
- Sanitize user inputs to reduce prompt injection risks
- Consider content filtering for inappropriate prompts/outputs

### Observability
- Track request latency and error rate to LiteMAAS
- Use structured logs (JSON) and include request IDs
- If possible, add tracing spans around LLM calls
