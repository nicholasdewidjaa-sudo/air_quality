# Test Error Handling

This file is used to verify that the application correctly handles erroneous input and server failures.

## Scenarios
1. **Invalid Sensor Payload** – Send a POST request with missing required fields and ensure the API returns a 400 Bad Request with a descriptive error message.
2. **Database Connection Loss** – Simulate a lost connection to PostgreSQL and verify the backend responds with a 503 Service Unavailable.
3. **External API Timeout** – Mock a timeout when fetching data from a public air‑quality API; the system should log the error and continue without crashing.
4. **WebSocket Disconnection** – Force a client to disconnect unexpectedly; the server must clean up resources without leaking memory.

## Expected Results
- Proper HTTP status codes (400, 503, etc.)
- JSON error body with `error` and `details` fields.
- No unhandled exceptions in server logs.
- Graceful recovery where possible.
