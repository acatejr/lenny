# Open WebUI with LiteLLM and PostgreSQL

This Docker Compose setup provides a complete AI chat interface using:
- **Open WebUI**: Modern web interface for AI conversations
- **LiteLLM**: Proxy server supporting multiple LLM providers
- **PostgreSQL**: Robust database backend for data persistence

## Quick Start

1. **Clone and setup environment**:
   ```bash
   # Copy the environment template
   cp .env.example .env
   
   # Edit .env with your API keys
   nano .env
   ```

2. **Start the services**:
   ```bash
   docker-compose up -d
   ```

3. **Access the application**:
   - Open WebUI: http://localhost:3000
   - LiteLLM API: http://localhost:4000
   - PostgreSQL: localhost:5432

## Configuration

### API Keys Setup

Edit the `.env` file with your actual API keys:

- **OpenAI**: Required for GPT models
- **Anthropic**: Required for Claude models  
- **Google**: Required for Gemini models

### Available Models

The configuration includes support for:
- OpenAI: GPT-3.5-turbo, GPT-4, GPT-4-turbo
- Anthropic: Claude-3-sonnet, Claude-3-haiku
- Google: Gemini-pro

### Security Considerations

**Important**: Change these default values in production:
- `WEBUI_SECRET_KEY`: Used for session encryption
- `POSTGRES_PASSWORD`: Database password
- `LITELLM_MASTER_KEY`: API authentication key

## Service Details

### Open WebUI (Port 3000)
- Web interface for AI conversations
- User authentication and management
- Chat history stored in PostgreSQL
- Configured to use LiteLLM as the model provider

### LiteLLM (Port 4000)
- Unified API for multiple LLM providers
- Handles authentication and routing
- Provides OpenAI-compatible API endpoints
- Configurable via `litellm_config.yaml`

### PostgreSQL (Port 5432)
- Persistent data storage
- User accounts and chat history
- Automatic health checks
- Data persisted in Docker volume

## Management Commands

### Start services
```bash
docker-compose up -d
```

### View logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f open-webui
docker-compose logs -f litellm
docker-compose logs -f postgres
```

### Stop services
```bash
docker-compose down
```

### Reset data (WARNING: Deletes all data)
```bash
docker-compose down -v
docker volume rm lenny_postgres_data lenny_open_webui_data
```

### Update services
```bash
docker-compose pull
docker-compose up -d
```

## Customization

### Adding New Models

Edit `litellm_config.yaml` to add new models:

```yaml
model_list:
  - model_name: your-model-name
    litellm_params:
      model: provider/model-name
      api_key: os.environ/YOUR_API_KEY
```

### Environment Variables

Key environment variables in the Docker Compose file:

**Open WebUI**:
- `DATABASE_URL`: PostgreSQL connection string
- `OPENAI_API_BASE_URL`: Points to LiteLLM proxy
- `ENABLE_SIGNUP`: Allow new user registration
- `DEFAULT_MODELS`: Models shown by default

**LiteLLM**:
- `LITELLM_MASTER_KEY`: API authentication
- `LITELLM_LOG`: Logging level

**PostgreSQL**:
- `POSTGRES_DB`: Database name
- `POSTGRES_USER`: Database user
- `POSTGRES_PASSWORD`: Database password

## Troubleshooting

### Common Issues

1. **Services won't start**: Check Docker and Docker Compose are installed
2. **API errors**: Verify API keys in `.env` file
3. **Database connection issues**: Ensure PostgreSQL is healthy
4. **Port conflicts**: Change port mappings if needed

### Health Checks

All services include health checks:
- PostgreSQL: Database connectivity
- LiteLLM: HTTP endpoint availability  
- Open WebUI: Application health

### Logs and Debugging

Enable verbose logging by setting:
```yaml
environment:
  - LITELLM_LOG=DEBUG
```

## Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Open WebUI    │───▶│     LiteLLM      │───▶│  LLM Providers  │
│   (Port 3000)   │    │   (Port 4000)    │    │ (OpenAI, etc.)  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
         │
         ▼
┌─────────────────┐
│   PostgreSQL    │
│   (Port 5432)   │
└─────────────────┘
```

## License

This configuration is provided as-is. Please check individual component licenses:
- [Open WebUI](https://github.com/open-webui/open-webui)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [PostgreSQL](https://www.postgresql.org/)
