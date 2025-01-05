
# Comprehensive Guide to Kong Metrics Collection

Kong is a powerful microservice API gateway that simplifies the management of APIs in complex microservice architectures. It acts as a centralized access point, handling operations such as security, load balancing, rate limiting, circuit breaking, and more. By aggregating APIs across various microservices, Kong provides a unified interface to external users while protecting backend services.

## Why Use Kong for Metrics?

Kong facilitates efficient monitoring and management of APIs, ensuring performance and reliability across services. To streamline monitoring, Kong integrates seamlessly with **Prometheus**, a leading monitoring and alerting toolkit. The Prometheus plugin, bundled with Kong, enables the collection and visualization of key performance metrics from Kong and its upstream services.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Setting Up the Prometheus Plugin

### Compatibility with DB-Less Mode

Kong's Prometheus plugin is fully compatible with DB-less mode, allowing you to configure Kong declaratively. This mode focuses on managing configurations via files rather than databases. Note that metrics related to database connectivity will always report a healthy status in DB-less mode.

### Enabling the Prometheus Plugin

You can enable the Prometheus plugin either through Kong’s **Admin API** or management tools like KongA. Below is an example of enabling the plugin via Admin API:

```bash
curl -X POST http://{HOST}:8001/plugins/ \
    --data "name=prometheus"
```

Alternatively, if you're using a management tool, navigate to the **Plugins** section, search for "Prometheus," and enable it.

### Plugin Configuration Parameters

The plugin allows several configurations for fine-tuning your metrics collection:

- **`name`**: (String) Plugin name, e.g., `prometheus`.
- **`service.id`**: (String) ID of the service to which the plugin is linked.
- **`enabled`**: (Boolean) Specifies whether the plugin is active. Default: `true`.
- **`config.per_consumer`**: (Boolean) Collect metrics per consumer. Default: `false`.

#### New Features in Version 1.4.x:
- **`data_plane_cluster_cert_expiry_timestamp`**: Tracks the expiration date of the data plane cluster certificate.
- **Subsystem Label for Upstream Targets**: Adds a `subsystem` label to health status metrics for better granularity.

---

## Metrics Available in Kong

The Prometheus plugin exposes several key metrics, enabling comprehensive monitoring of Kong and its upstream services. Below is a breakdown of the most important metrics:

### 1. **Status Codes**
   - Tracks HTTP response codes from upstream services.
   - Metrics are available for:
     - Individual services
     - All services
     - Specific consumers

### 2. **Latency Histograms**
   - Measures time delays across various stages of a request:
     - **Request**: Total time for Kong and the upstream service to process the request.
     - **Kong**: Time taken by Kong to route requests and execute plugins.
     - **Upstream**: Time taken by upstream services to process the request.

### 3. **Bandwidth**
   - Tracks total data ingress and egress through Kong for individual services and as a whole.

### 4. **Database Reachability**
   - A binary metric (0 or 1) indicating whether Kong can connect to the database.

### 5. **Nginx Connections**
   - Metrics related to Nginx connections, including active, reading, writing, and idle connections.

### 6. **Target Health**
   - Monitors the health status of upstream targets, including:
     - Healthy
     - Unhealthy
     - DNS errors
     - Health checks disabled

### 7. **Data Plane Status**
   - Tracks data plane metrics, including:
     - Last reported timestamp
     - Configuration hash
     - Certificate expiry timestamps

### 8. **Enterprise License Information**
   - Displays Kong Gateway license details, including expiration and enabled features.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Example Metrics Output

You can fetch Kong’s Prometheus metrics by querying the `/metrics` endpoint. Below is a sample output:

```bash
curl -i http://localhost:8001/metrics
```

Sample response:
```plaintext
# HELP kong_bandwidth Total bandwidth in bytes consumed per service/route in Kong
# TYPE kong_bandwidth counter
kong_bandwidth{type="egress",service="google",route="google.route-1"} 1277
kong_bandwidth{type="ingress",service="google",route="google.route-1"} 254

# HELP kong_http_status HTTP status codes per service/route in Kong
# TYPE kong_http_status counter
kong_http_status{code="200",service="service-1",route="route-1"} 15

# HELP kong_latency Latency added by Kong, total request time and upstream latency
# TYPE kong_latency histogram
kong_latency_bucket{type="request",service="service-1",route="route-1",le="00300.0"} 8
kong_latency_bucket{type="kong",service="service-1",route="route-1",le="00001.0"} 2
```

### Key Sections in the Metrics Output

1. **Bandwidth Metrics**: Provides insights into total traffic flow.
2. **Latency Metrics**: Highlights performance bottlenecks between Kong and upstream services.
3. **Status Code Metrics**: Tracks success and error rates across services.
4. **Connection Metrics**: Monitors Nginx server performance.

---

## Conclusion

Kong’s Prometheus plugin offers a robust and scalable solution for monitoring microservices. By exposing detailed metrics, it enables real-time tracking of performance, health, and traffic across APIs and services. Whether you’re working in a DB-less or database-backed mode, the plugin ensures seamless integration with Prometheus for advanced observability.

For organizations leveraging Kong as their API gateway, integrating Prometheus ensures proactive issue detection and performance optimization. Start collecting metrics today to unlock the full potential of your microservice architecture!
