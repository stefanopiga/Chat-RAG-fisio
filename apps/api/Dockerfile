FROM python:3.11-slim-bullseye

# Imposta variabili d'ambiente per ottimizzare Python
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# Imposta la directory di lavoro
WORKDIR /app

# Installa Poetry
RUN pip install poetry

# Copia i file delle dipendenze e installale
# Questo passaggio viene messo in cache da Docker
COPY apps/api/poetry.lock apps/api/pyproject.toml ./
RUN poetry config virtualenvs.create false && poetry install --no-root

# Copia il codice dell'applicazione
COPY ./apps/api/api /app/api

# Espone la porta su cui Uvicorn girerà
EXPOSE 8000

# Comando per avviare l'applicazione
CMD ["uvicorn", "api.main:app", "--host", "0.0.0.0", "--port", "8000"]
