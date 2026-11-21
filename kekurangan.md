# Kekurangan (Limitations)

1. **Data Source Reliability** – The accuracy of the displayed air‑quality metrics depends on the reliability of external sensor networks and public APIs. Incomplete or delayed data can affect visualizations.
2. **Scalability** – The current design uses a single PostgreSQL instance; handling a massive number of sensors across multiple cities may require sharding or a dedicated time‑series database cluster.
3. **Real‑time Performance** – Live updates are pushed via WebSocket. Under high load (many concurrent users), server resources may become a bottleneck.
4. **User Authentication** – Basic authentication is implemented; advanced role‑based access control (e.g., admin vs. public viewer) is not yet in place.
5. **Mobile Experience** – The UI is responsive but has not been thoroughly tested on low‑end mobile devices; performance may degrade.
6. **Alert Customization** – Users can receive generic alerts, but fine‑grained customization (e.g., specific pollutant thresholds per location) is missing.
7. **Offline Support** – No offline caching; users lose access when network connectivity is intermittent.

*These points identify areas for future improvement and further development.*