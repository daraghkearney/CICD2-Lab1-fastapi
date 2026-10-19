# CICD-2 FastAPI Lab Template

This repository contains a FastAPI lab for the CICD-2 module.  
Use this `README.md` as the standard structure for future labs.

---

## Lab Title

**Replace this with the lab title**

Example:

> Lab 1 — Creating a Basic FastAPI Application

---
## Repository Structure

Use the following structure for the labs:

```text
project-name/
│
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── schemas.py
│   ├── services.py
│   └── models.py
│
├── tests/
│   ├── __init__.py
│   └── test_main.py
│
├── requirements.txt
├── README.md
└── .gitignore
```

## Lab Title

**Lab 5 — Two FastAPI Microservices with HTTPX**

## GET /api/products/{product_id}

The User API exposes `GET /api/products/{product_id}` on port **8000**. It does not store products. The route calls `fetch_product()` in `app/product_client.py`, which sends `GET /products/{product_id}` to the Product Service.

| Request | Result |
|---|---|
| `GET /api/products/1` while both services are running | 200 and the product JSON (`id`, `name`, `price`) |
| `GET /api/products/99` | 404 `Product not found` |
| Product Service is not running | 503 `Product service unavailable` |

The Product Service itself listens on port **8001** at `GET /products/{product_id}`. In the Codespaces Ports tab, open port 8000 `/docs` to try the User API route, and port 8001 `/docs` to try the Product Service route directly.

## Run both services

Use two Codespaces terminals. Leave each process running.

Terminal 1 — Product Service, port 8001:

```bash
python -m uvicorn product_service.main:app --host 0.0.0.0 --port 8001 --reload

Terminal 2 — User API, port 8000:

```bash
python -m uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload

--host 0.0.0.0 is only the address Uvicorn binds to. Requests from the User API still use http://127.0.0.1:8001. Stop both processes before running python -m pytest. The tests replace the product client and do not need Product Service to be running.

**PRODUCT_SERVICE_URL**
PRODUCT_SERVICE_URL is the base URL of the Product Service. app/product_client.py reads it from the environment and defaults to http://127.0.0.1:8001, which is correct while both processes run in one Codespace. fetch_product() calls {PRODUCT_SERVICE_URL}/products/{product_id}.

The route does not hardcode that host. Next week, Docker Compose can set PRODUCT_SERVICE_URL=http://product-service:8001 for the User API container without changing the endpoint or the client. Inside a container, 127.0.0.1 would point at that container, not at Product Service.