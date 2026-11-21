# Alur Kerja Web Application Air Quality Monitoring

## 1. Data Ingestion (Backend)
1. **Sensor / API Collectors** – Scheduled jobs (cron) fetch raw data from:
   - Local IoT sensor endpoints (HTTP POST/GET)
   - Public air‑quality APIs (e.g., OpenAQ, AirNow)
2. **Normalization** – Convert all incoming payloads to a unified JSON schema:
   ```json
   {
     "sensor_id": "string",
     "timestamp": "ISO8601",
     "pm2_5": "float",
     "pm10": "float",
     "co2": "float",
     "no2": "float",
     "location": {"lat": "float", "lon": "float"}
   }
   ```
3. **Validation & Enrichment** – Verify required fields, filter outliers, add geographic lookup (city, region).
4. **Storage** – Insert into PostgreSQL (TimescaleDB hypertable) for efficient time‑series queries.

## 2. API Layer
- **REST Endpoints** (FastAPI/Express):
  - `GET /api/sensors` – list sensors & metadata
  - `GET /api/readings?sensor_id=&from=&to=` – fetch time‑range data
  - `GET /api/summary?region=&period=` – aggregated statistics
- **WebSocket** – Push new readings to connected clients for live map updates.

## 3. Business Logic
- **Aggregation Service** – Periodically compute hourly/daily averages, max/min values, and generate alerts when thresholds are crossed.
- **Alert Service** – Sends email/SMS/Push notifications via external providers (e.g., SendGrid, Twilio).

## 4. Frontend Rendering
1. **Initial Load** – SPA fetches sensor list and recent readings via the REST API.
2. **Map View** – Leaflet/Mapbox displays markers; color‑coded based on latest PM2.5 value.
3. **Dashboard** – Chart.js or D3 renders time‑series charts for selected sensor/location.
4. **Real‑time Updates** – WebSocket listener updates map markers and charts instantly.
5. **User Interaction** – Filters (date range, pollutant type), detail pop‑ups, and alert subscription UI.

## 5. Deployment Pipeline
- **Dockerfile** – Separate images for `backend`, `frontend`, and `db`.
- **docker‑compose.yml** – Orchestrates containers, sets environment variables, and creates a network.
- **CI/CD** – GitHub Actions run linting, unit tests, build Docker images, and push to registry; deploy to staging/production server.

## 6. Teknologi & Bahan‑bahan
- **Backend**: Python 3.11, FastAPI, SQLAlchemy, TimescaleDB (PostgreSQL extension)
- **Frontend**: React, Tailwind CSS, Leaflet, Chart.js
- **Messaging**: Redis (pub/sub) for WebSocket broadcasting
- **Containerization**: Docker, Docker‑Compose
- **Monitoring**: Prometheus + Grafana for service health
- **Version Control**: Git

---
*Dokumen ini menggambarkan alur kerja end‑to‑end mulai dari pengambilan data hingga penyajian di UI, serta teknologi yang terlibat.*