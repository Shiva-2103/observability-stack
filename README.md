# Observability Stack Documentation

---

### 📌 Overview:

This document outlines the installation, configuration, and troubleshooting steps for setting up an observability stack comprising:

* **Prometheus** – Metrics collection
* **Grafana** – Visualization
* **Loki** – Log aggregation
* **Promtail** – Log shipping
* **Node Exporter** – System metrics

---

### ⚙️ Stack Components & Purpose

| Component     | Purpose                         |
| ------------- | ------------------------------- |
| Prometheus    | Scrapes metrics from services   |
| Grafana       | Visualizes metrics and logs     |
| Loki          | Stores and indexes logs         |
| Promtail      | Collects logs and ships to Loki |
| Node Exporter | Provides host-level metrics     |

---

### 🏗️ Stack Setup Using Docker Compose

1. **Folder Structure:**

   ```bash
   observability-stack/
   ├── docker-compose.yml
   ├── prometheus/
   │   └── prometheus.yml
   ├── grafana/
   │   └── provisioning/...
   ├── loki/
   │   └── local-config.yaml
   └── promtail/
       └── config.yml
   ```

2. **Running the Stack:**

   ```bash
   docker-compose up -d
   ```

3. **Check Logs:**

   ```bash
   docker logs <container_name>
   ```

---

### 🔍 Common Issues & Fixes

#### Loki Errors:

* **Config Parsing Errors:**

  * Reason: Invalid keys like `final_sleep`, `enforce_metric_name`, or outdated `schema_config`
  * Fix: Remove or update deprecated keys, use correct schema version

* **Permission Denied:**

  * Reason: Loki can't write to volume (e.g., `/loki/chunks`)
  * Fix:

    ```yaml
    volumes:
      - ./loki:/loki
    ```

    Ensure folder permissions are appropriate:

    ```bash
    sudo chmod -R 777 ./loki
    ```

#### Promtail Binding Mount Error:

* Error: `not a directory` while mounting `config.yml`
* Fix: Ensure you're mounting a **file** not a **directory**:

  ```yaml
  volumes:
    - ./promtail/config.yml:/etc/promtail/config.yml
  ```

#### Grafana Setup:

* **Default credentials:**

  * `admin/admin`
  * Change on first login

* **Import Dashboard:**

  * Open Grafana → Dashboards → Import → Use ID from [Grafana.com](https://grafana.com/grafana/dashboards)

---

### 📊 Sample Prometheus Job Configuration

```yaml
scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']
  - job_name: 'webapp'
    static_configs:
      - targets: ['webapp:5000']
```

---

### 🌐 List IPs and Ports

**List IP Addresses:**

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_name>
```

**List All Container Ports:**

```bash
docker ps
```

---

### ✅ Verification Checklist

* [ ] Prometheus UI accessible at `localhost:9090`
* [ ] Grafana UI accessible at `localhost:3000`
* [ ] Node Exporter metrics available
* [ ] Logs visible in Loki from Promtail
* [ ] Dashboards display metrics/logs correctly

---

### 📦 Recommendations

* Pin image versions in Docker Compose
* Backup Grafana dashboards and Loki data regularly
* Use Grafana Alerts for real-time monitoring

---

### 🧹 Cleanup

```bash
docker-compose down -v
```


