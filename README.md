# SmartCache: AI-Powered Dynamic TTL Caching System

![System Architecture](<img width="2014" height="692" alt="image" src="https://github.com/user-attachments/assets/adea67ac-d9e3-4e92-8221-4fd6ee0ad185" />)

**SmartCache** is an AI-powered caching system that optimizes resource caching by using dynamic Time-To-Live (TTL) values.

Unlike traditional caching systems that rely on static TTLs, SmartCache utilizes **LSTM-based prediction models** and time-series data to predict the optimal lifetime for each cache entry.

## 🚀 Key Features

* **Dynamic TTL Assignment:** Automatically assigns per-resource TTL based on AI predictions rather than static configuration.
* **Predictive Caching:** Leverages historical access patterns to forecast resource popularity and adjust cache duration accordingly.
* **Event-Driven Architecture:** Built on a Kafka-based pipeline to ensure high scalability and component decoupling.
* **Plug-and-Play SDK:** Provides a generic SDK allowing developers to integrate predictive caching with minimal code changes.
* **Dockerized Stack:** The entire microservices suite is deployable via a single `docker-compose` command.

---

## 🏗️ System Architecture

The system is organized into three distinct layers:

### 1. Client Layer

* User applications integrate via the **SmartCache SDK**.
* `set()` calls interact with the SmartCache system to initialize predictive TTLs.
* `get()` calls function as standard Redis lookups to ensure low latency.

### 2. Processing Layer

* **Kafka:** Acts as the event streaming backbone.
* **Go Log Consumer:** Ingests access logs into TimescaleDB/Postgres.
* **Go TTL Updater:** Periodically requests TTL predictions and updates Redis keys.

### 3. AI & Storage Layer

* **Python LSTM Service:** Analyzes time-series access logs to predict optimal TTLs.
* **Redis:** Stores the actual cached resources.
* **TimescaleDB/Postgres:** Stores historical logs for model training.

---

## 🛠️ Installation & Deployment

SmartCache is designed as a set of Dockerized microservices.

### Prerequisites

* Docker & Docker Compose

### Quick Start

To deploy the complete stack (Redis, Kafka, Zookeeper, TimescaleDB, Go Consumers, and Python Predictor):

```bash
# Clone the repository
git clone https://github.com/sathwikshetty33/SmartCache.git

# Navigate to the directory
cd smartcache

# Start the services
docker-compose up -d
```

---

## 💻 SDK Usage

Integrating SmartCache into your Python application is straightforward.

### Example Python

```python
from smartcache import SmartCache

# Initialize the client
cache = SmartCache(
    redis_host="localhost",
    redis_port=6379,
    smartcache_host="localhost",
    smartcache_port=5000
)

# Set a value (Logs access and sets initial dynamic TTL)
cache.set("video:123", "data_payload")

# Get a value (Standard Redis retrieval)
value = cache.get("video:123")
```

---

## 🔄 How It Works

1. **Initial Set:** The user calls `cache.set()`. An initial short TTL is set, and an access log is sent to Kafka.

2. **Log Processing:**
   * The Go Log Consumer stores the event in TimescaleDB.
   * The Go TTL Updater requests a prediction.

3. **Prediction:** The Python LSTM Service analyzes historical access patterns for that specific key.

4. **Update:** The TTL Updater dynamically adjusts the expiration time of the key in Redis based on the model's output.

---

## 📊 Use Cases

* **API Response Caching:** optimizing storage for varying endpoint popularity.
* **CDN Content:** Managing static and dynamic assets.
* **Database Queries:** Caching complex query results.
* **Edge Computing:** Efficient resource management in serverless environments.

---

## 📈 Evaluation Metrics

The system performance is evaluated based on:

* Cache Hit Ratio
* Average Response Latency
* Backend Load Reduction
* Prediction Accuracy of the LSTM model

---
