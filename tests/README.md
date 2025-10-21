# Integration Tests

Test di integrazione cross-app.

## Struttura

- `integration/` - Test integrazione tra componenti (API + database + Celery)

## Esecuzione

```bash
# Run all integration tests
pytest tests/integration/ -v

# Run specific test
pytest tests/integration/test_ingestion_pipeline.py -v
```

## Note

I test unitari specifici per app sono in:
- `apps/api/tests/` - Backend unit/integration tests
- `apps/web/tests/` - Frontend unit/E2E tests
