# ⚡️ mcpo-php

Expose any MCP tool as an OpenAPI-compatible HTTP server—instantly.

**mcpo-php** is a PHP implementation of the [mcpo](https://github.com/open-webui/mcpo) idea: a dead-simple proxy that takes an MCP server command and makes it accessible via standard RESTful OpenAPI, so your tools "just work" with LLM agents and apps expecting OpenAPI servers.

No custom protocol. No glue code. No hassle.

---

## 🤔 Why Use mcpo Instead of Native MCP?

MCP servers usually speak over raw stdio, which is:

- 🔓 Inherently insecure  
- ❌ Incompatible with most tools  
- 🧩 Missing standard features like docs, auth, error handling, etc.  

**mcpo-php** solves all of that—without extra effort:

- ✅ Works instantly with OpenAPI tools, SDKs, and UIs  
- 🛡 Adds security, stability, and scalability using trusted web standards  
- 🧠 Auto-generates interactive docs for every tool, no config needed  
- 🔌 Uses pure HTTP—no sockets, no glue code, no surprises  

What feels like "one more step" is really fewer steps with better outcomes.  
mcpo makes your AI tools usable, secure, and interoperable—right now, with zero hassle.

---

## 🚀 Features

- Dynamic proxy: exposes MCP servers as REST endpoints  
- Auth modes:  
  - `none` (no auth)  
  - `bearer` (static API key)  
  - `pkce` (OAuth 2.0 with PKCE, refresh, persistence)  
- Token store with expiry + auto-refresh  
- Basic management UI for viewing and managing auth  
- Deploy via Docker or bare PHP  
- Minimal config: one JSON file  

---

## 🏗 Tech Stack

- **PHP 8.2+**  
- **ReactPHP** (event loop + HTTP server)  
- **php-mcp/server** + **php-mcp/client**  
- **league/oauth2-client** for OAuth2/PKCE  
- JSON config & token store  
- Simple HTML UI  

---

## 📂 Project Structure

```

mcpo-php/
bin/
server.php              # Entrypoint for the proxy
src/
Proxy/
ProxyServer.php
AuthHandler.php
TokenStore.php
OAuth/
PkceClient.php
config/
mcpo.json               # Define MCP servers + auth modes
storage/
tokens.json             # Token persistence
public/
index.php               # Minimal management UI

````

---

## ⚙️ Configuration

Edit `config/mcpo.json` to define MCP servers:

```json
{
  "mcpServers": {
    "socialcocafe": {
      "type": "streamable-http",
      "url": "http://mcp-php:8080/mcp",
      "auth": "none"
    },
    "todoist": {
      "type": "streamable-http",
      "url": "https://ai.todoist.net/mcp",
      "auth": "pkce",
      "client_id": "tdd_xxxxx",
      "authorize_url": "https://app.todoist.com/oauth/authorize",
      "token_url": "https://app.todoist.com/oauth/access_token",
      "redirect_uri": "http://localhost:39725/oauth/callback",
      "scopes": ["data:read", "data:write"]
    }
  }
}
````

* `none` → no auth headers
* `bearer` → static API key (supply in config)
* `pkce` → full OAuth2 PKCE flow with auto-refresh

---

## ▶️ Running

### Bare PHP

```bash
composer install
php bin/server.php
```

Server starts on port `8000` by default.

### Docker

```bash
docker-compose up --build
```

Mounts config + storage volumes for persistence.

---

## 🌐 Management UI

Visit:

```
http://localhost:8000/
```

From here you can:

* View configured servers
* Check auth status (✅ valid, ⚠️ expired, ⛔ missing)
* Start PKCE flowHere’s a fleshed-out `README.md` for your **mcpo-php** project. It captures the positioning you laid out, but also grounds it in developer-friendly docs so someone can actually spin this up and see results fast.

---

```markdown

* Refresh or revoke tokens

---

## 🔑 Authentication Modes

### None

No `Authorization` header injected.

### Bearer

Static header injection:

```
Authorization: Bearer <your_api_key>
```

### PKCE

1. Start PKCE flow from UI
2. mcpo-php opens authorization URL in browser
3. Local callback exchanges code for tokens
4. Tokens persisted in `storage/tokens.json`
5. Auto-refresh when expired

---

## 🐳 Example `docker-compose.yml`

```yaml
version: "3.9"
services:
  mcpo:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - ./config:/app/config
      - ./storage:/app/storage
```

---

## 📜 Roadmap

* [ ] Multi-tenant support
* [ ] Hot reload config
* [ ] Pluggable token backends (Redis, DB)
* [ ] Metrics + logs

---

## 🤝 Contributing

1. Fork repo
2. Create feature branch (`git checkout -b feature/my-feature`)
3. Commit changes (`git commit -m 'Add new feature'`)
4. Push to branch
5. Open PR

---

## 📄 License

MIT — free to use, hack, and deploy.

