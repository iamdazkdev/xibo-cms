---
description: Convert Swagger API spec into per-service markdown docs in docs/API_DOCS/. Read JSON → generate markdown tables.
---

# Swagger to API Docs Workflow

> **🤖 AI Persona Injection:**
> BEFORE executing any steps below, you MUST read `view_file` on `app/frontend/rmn_app/.claude/agents/flutter-architect.md` and strictly adopt that persona!

Read Swagger JSON specs and convert them into frontend-friendly API documentation markdown files.

// turbo-all

## When to Use

- New backend service deployed with Swagger
- Backend adds/removes/changes endpoints
- Need to refresh all API docs from live Swagger
- Onboarding new developer — regenerate docs from source of truth

## Source

**Swagger UI**: [dev-api.mtsvn.io/swagger](https://dev-api.mtsvn.io/swagger/#/)
**Per-service JSON**: `https://dev-api.mtsvn.io/swagger/{service}/doc.json`

### Known Services

| Service | Slug | JSON URL |
|---------|------|----------|
| Auth | `auth` | `https://dev-api.mtsvn.io/swagger/auth/doc.json` |
| User | `user` | `https://dev-api.mtsvn.io/swagger/user/doc.json` |
| Business | `business` | `https://dev-api.mtsvn.io/swagger/business/doc.json` |
| Device | `device` | `https://dev-api.mtsvn.io/swagger/device/doc.json` |
| Campaign | `campaign` | `https://dev-api.mtsvn.io/swagger/campaign/doc.json` |
| CMS | `cms` | `https://dev-api.mtsvn.io/swagger/cms/doc.json` |
| Role | `role` | `https://dev-api.mtsvn.io/swagger/role/doc.json` |
| Supplier | `supplier` | `https://dev-api.mtsvn.io/swagger/supplier/doc.json` |

## Output Format

Each service produces a file: `docs/API_DOCS/{service}-service.md`

### File Structure Template

```markdown
# {Service Title} Service

**Source**: `app/backend/services/{service}-service`
**Swagger**: [dev-api.mtsvn.io/swagger/{slug}](https://dev-api.mtsvn.io/swagger/{slug}/doc.json)
**Base Path**: `/api/v1`

| Method | Endpoint | Auth | Summary |
|--------|----------|------|---------|
| {METHOD} | `{path}` | 🔒/❌ | {summary} |

---

## {METHOD} {path}

> {🔒 if auth required} {description}

**Request Body** — `{SchemaName}`:

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `{field}` | {type} | ✅/❌ | {validation rules} |

**Query Parameters** (for GET with params):

| Param | Type | Description |
|-------|------|-------------|
| `{param}` | {type} | {description} |

**Response** (`{status_code} {status_text}`) — `{ResponseSchemaName}`:

| Field | Type | Description |
|-------|------|-------------|
| `{field}` | {type} | {description} |

**Errors**: `{error_codes}`

---
```

## Steps

1. **Determine scope** — which service(s) to convert:
   - User specifies service name → convert that 1 service
   - User says "all" → convert all known services
   - If unsure → ask user

2. **Fetch Swagger JSON** for each service:
   - Use `read_url_content` on `https://dev-api.mtsvn.io/swagger/{slug}/doc.json`
   - Read ALL chunks to get the complete spec (paths + definitions)

3. **Parse the Swagger spec**:
   - Extract `info.title` → service title
   - Extract `basePath` → base path
   - Extract all `paths` → endpoint list
   - For each path + method:
     - `summary` → Summary column
     - `security` → Auth column (🔒 if `Bearer` security, ❌ if none)
     - `parameters` (in: body) → Request Body table
     - `parameters` (in: query) → Query Parameters table
     - `parameters` (in: path) → Note in endpoint path as `{param}`
     - `responses.200.schema` → Response table
   - Resolve `$ref` → look up in `definitions` section
   - For each definition, extract `properties` → field tables

4. **Build the markdown** following the Output Format above:
   - Header with source, swagger link, base path
   - Summary table listing ALL endpoints
   - Detailed section for EACH endpoint with:
     - Description (from `description` field)
     - Request body fields (from resolved `$ref` definitions)
     - Query parameters (from `parameters` with `in: query`)
     - Response fields (from resolved response `$ref` definitions)
     - Error status codes (from non-200 response keys)
   - Shared schema section at bottom (for reusable response types like `ContentResp`)

5. **Write the markdown file**:

   ```
   docs/API_DOCS/{service}-service.md
   ```

6. **Update api-reference.md** (if new service or endpoint counts changed):
   - Update the Service Index table
   - Update the total endpoint count

7. **Verify** the generated docs:
   - Check all endpoints are documented
   - Check all request/response schemas are resolved
   - No `$ref` left unresolved
   - Consistent formatting with existing docs

## Mapping Rules

### Swagger → Markdown

| Swagger Field | Markdown Output |
|---------------|-----------------|
| `paths[path][method]` | `## {METHOD} {path}` section |
| `summary` | Summary table + section header |
| `description` | Blockquote under section header |
| `security: [{ Bearer: [] }]` | 🔒 in Auth column |
| No `security` | ❌ in Auth column |
| `parameters[in=body].schema.$ref` | Resolve → Request Body table |
| `parameters[in=query]` | Query Parameters table |
| `responses.200.schema.$ref` | Resolve → Response table |
| `responses.{4xx}` | Error codes list |
| `definitions[name].properties` | Field tables (Type, Required from `required[]`) |
| `definitions[name].properties[field].enum` | Note enum values in Validation column |
| `definitions[name].properties[field].minimum` | Note min value in Validation column |
| `definitions[name].properties[field].maxLength` | Note max length in Validation column |

### Type Mapping

| Swagger Type | Markdown Type |
|-------------|---------------|
| `string` | string |
| `integer` | int |
| `number` | number |
| `boolean` | bool |
| `array` of `$ref` | `{TypeName}[]` |
| `array` of `string` | `string[]` |
| `object` | object |

## Example

User says: "Sync auth service API docs from swagger"

1. Fetch `https://dev-api.mtsvn.io/swagger/auth/doc.json`
2. Read all chunks, parse paths + definitions
3. Generate `docs/API_DOCS/auth-service.md` with:
   - 16 endpoints in summary table
   - Detailed sections for each endpoint
   - Resolved schemas: `LoginEmailReq`, `LoginResp`, `RefreshTokenReq`, etc.
4. Update `api-reference.md` endpoint count if changed

## Rules

- ⛔ NEVER invent fields — only document what Swagger spec contains
- ⛔ NEVER skip endpoints — document ALL paths
- ✅ Always resolve `$ref` to show actual field tables
- ✅ Always include the Standard Response Envelope note
- ✅ Keep consistent formatting with existing docs in `docs/API_DOCS/`
- ✅ Preserve existing human-written notes/comments if updating (check diff)
- ✅ If a definition is shared across multiple endpoints, document it once at the bottom as a "Full Schema" section
