# Service Registry Homework (Week 7)

A simple Python-based service registry and client demo for microservice discovery, health checks, and lifecycle management.

## Repository structure

- `HW/service_registry_improved.py`: Flask-based registry server with endpoints `/register`, `/discover/<service>`, `/deregister`, `/heartbeat`, `/services`, `/health`.
- `HW/example_service.py`: Example client/service that registers itself, sends recurring heartbeats, can deregister on shutdown, and includes simple discovery/demo helpers.
- `HW/requirements.txt`: Python dependencies.

## Features

- register service instances with address and heartbeat tracking
- discover active instances per service
- deregister instances on shutdown
- automatic active instance selection via heartbeat timeout
- in-memory registry with thread-safety (`threading.Lock`)
- client demo for local microservice behavior

## Requirements

- Python 3.8+
- `pip install -r HW/requirements.txt`

## Run the registry server

```bash
cd "HW"
python service_registry_improved.py
```

> By default, Flask app uses built-in development port 5000. Set `FLASK_APP=service_registry_improved.py` and use `flask run` if needed.

## Run 2 example service instance

```bash
cd "HW"
python example_service.py user-service 8001
```
On a new terminal  

```bash
cd "HW"
python example_service.py user-service 8001
```

The service registers itself at `http://localhost:5001` by default (the registry URL in this exercise). It then sends a heartbeat every 10s and deregisters on Ctrl+C.

### Example usage

- `python example_service.py <service_name> <port>`
  - registers as `http://localhost:<port>`
  - keeps running and sends heartbeat
  - Ctrl+C triggers graceful deregistration

- `python example_service.py demo`
  - calls `/health` and `/services`, showcases registry state

- `python example_service.py demo-rand`
  - queries `/discover/user-service` repeatedly and prints random instance

## API Endpoints (service registry)

- `POST /register`
  - body: `{ "service": "<name>", "address": "<url>" }`
  - registers or refreshes heartbeat for instance

- `GET /discover/<service>`
  - returns currently active instances (last heartbeat < 30s)

- `POST /deregister`
  - body: `{ "service": "<name>", "address": "<url>" }`
  - removes instance from registry

- `POST /heartbeat`
  - body: `{ "service": "<name>", "address": "<url>" }`
  - updates last heartbeat timestamp

- `GET /services`
  - lists all known services with total and active counts

- `GET /health`
  - returns `{"status": "healthy"}`

## Notes

- This exercise uses in-memory data in `registry` so data resets when server restarts.
- `HEARTBEAT_TIMEOUT=30` and `CLEANUP_INTERVAL=10` are set in `service_registry_improved.py`.
- `example_service.py` points to `http://localhost:5001` and expects registry on that port.

## Validation

1. Start registry
2. Start one or more services
3. Inspect `http://localhost:5001/services`
4. `curl http://localhost:5001/discover/user-service`

