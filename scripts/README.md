# FisioRAG Operational Scripts

Directory contenente script operativi per amministrazione, ingestion e maintenance.

## Prerequisites

```bash
# Install dependencies
pip install -r scripts/requirements.txt
```

**Environment Variables Required**:
- `SUPABASE_JWT_SECRET` - JWT signing secret (Supabase Dashboard → API Settings)
- `SUPABASE_SERVICE_KEY` - Service role key (Supabase Dashboard → API Settings)
- `OPENAI_API_KEY` - OpenAI API key
- `DATABASE_URL` - PostgreSQL connection URL (optional, for verify_ingestion.py)
- `API_BASE_URL` - FisioRAG API base URL (default: http://localhost)

**Configuration**:
1. Copy `.env.example` to `.env` in project root
2. Fill in actual values for required variables
3. Never commit `.env` file

## Admin Scripts

### Generate Admin JWT Token

Generate temporary JWT token for admin API operations.

```bash
python scripts/admin/generate_jwt.py --email admin@fisiorag.local --expires-days 7
```

**Arguments**:
- `--email`: Admin email for JWT subject (default: from ADMIN_EMAIL env)
- `--expires-days`: Token expiration in days (default: 365)

**Output**: JWT token string + export commands

**Usage**:
```bash
# Generate token
TOKEN=$(python scripts/admin/generate_jwt.py --expires-days 1)

# Use token in API call
curl -X POST http://localhost/api/v1/admin/knowledge-base/sync-jobs \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"document_text": "...", "metadata": {...}}'
```

## Ingestion Scripts

### Ingest Single Document

Ingest a single document into FisioRAG knowledge base.

```bash
python scripts/ingestion/ingest_single_document.py path/to/document.docx
```

**Arguments**:
- `document_path`: Path to document file (.docx, .txt, .md)
- `--api-url`: API base URL (default: http://localhost)
- `--admin-email`: Admin email for JWT (default: from ADMIN_EMAIL env)

**Supported Formats**:
- `.docx` (Microsoft Word)
- `.txt` (plain text)
- `.md` (Markdown)

**Example**:
```bash
python scripts/ingestion/ingest_single_document.py \
  conoscenza/fisioterapia/lombare/1_Radicolopatia_Lombare_COMPLETA.docx
```

**Output**: Job ID + ingestion result

### Verify Ingestion

Verify document ingestion success in database.

```bash
python scripts/ingestion/verify_ingestion.py <job_id>
```

**Arguments**:
- `job_id`: Job ID from ingestion response
- `--database-url`: PostgreSQL connection URL (default: from DATABASE_URL env)

**Example**:
```bash
# Ingest document and capture job_id
RESPONSE=$(python scripts/ingestion/ingest_single_document.py test.docx)
JOB_ID=$(echo $RESPONSE | jq -r '.job_id')

# Verify ingestion
python scripts/ingestion/verify_ingestion.py $JOB_ID
```

**Output**: Document info + chunk count

## Maintenance Scripts

(Coming soon)

## Troubleshooting

### Error: "SUPABASE_JWT_SECRET not found in environment"

**Cause**: `.env` file not found or missing required variable

**Solution**:
1. Create `.env` file in project root
2. Copy content from `.env.example`
3. Fill in actual values from Supabase Dashboard

### Error: "AuthenticationError: Error code: 401"

**Cause**: Invalid OpenAI API key

**Solution**:
1. Verify `OPENAI_API_KEY` in `.env`
2. Check key validity in OpenAI Platform
3. Ensure no trailing spaces in key value

### Error: "ConnectionError: Failed to connect to API"

**Cause**: API server not running or wrong URL

**Solution**:
1. Verify API is running: `docker ps | grep fisio-rag-api`
2. Check API_BASE_URL in `.env` (should be http://localhost if local)
3. Test API health: `curl http://localhost/health`
