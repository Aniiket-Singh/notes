deployment
![[./attachments/Pasted image 20250531110607.png]]

building


![[./attachments/Pasted image 20250711202940.png]]

---

# Monitoring

why do we not scrape metric in auto scaling groups by directing prometheus to load balancer? but instead are supposed to direct it to scrape data from every individual host in that group (fixed scale)

When you point Prometheus at your load‑balancer (LB) instead of each backend instance, you end up with:

1. **Aggregated (or incomplete) metrics**
    
    - The LB usually **proxies** one request at a time to one of the healthy instances. If you scrape `/metrics` through the LB, you only see whatever that _one_ instance happened to serve your scrape request—so you miss all the other instances’ metrics and get a biased sample (e.g. only 50 % of updates) ([Google Groups](https://groups.google.com/g/prometheus-users/c/7z4QCdqt5gc?utm_source=chatgpt.com "Prometheus for scraping multiple instances in a load balancer.")).
        
    - In contrast, scraping each instance directly guarantees you collect _every_ instance’s metrics.
        
2. **Loss of per‑instance context & labels**
    
    - Prometheus attaches `job` and `instance` labels to every scrape, so you can write queries like `avg(rate(http_requests_total[5m])) by (instance)` or alert on a single server’s `up == 0` ([prometheus.io](https://prometheus.io/docs/concepts/jobs_instances/?utm_source=chatgpt.com "Jobs and instances | Prometheus")).
        
    - If you scrape the LB, all metrics get the same `instance="lb.example.com:9100"`, and you lose the ability to distinguish—and thus monitor—the health and performance of each server.
        
3. **Inability to detect “one of many” failures**
    
    - One of Prometheus’s great strengths is writing expressions like:
        
        ```yaml
        alert: SomeBackendsDown
        expr: avg(up{job="webapp_backend"}) * 100 < 100
        ```
        
        which fires only when _more than one_ instance is down. Scraping via the LB gives you a single `up` metric and you can’t tell if all, some, or just one backend has failed ([Google Groups](https://groups.google.com/g/prometheus-users/c/7z4QCdqt5gc?utm_source=chatgpt.com "Prometheus for scraping multiple instances in a load balancer.")).
        
4. **Dynamic discovery vs. static LB endpoint**
    
    - Auto Scaling Groups (ASGs) spin instances up and down. Rather than manually reconfiguring Prometheus every time, you use a **service discovery** mechanism (AWS EC2 SD, Consul, Kubernetes, etc.) so Prometheus automatically scrapes each new or terminated instance.
        
    - Pointing at the LB doesn’t tell you _which_ backends exist or their individual lifecycle events—so you miss critical state changes.
        

---

### Best Practice: Direct‐Instance Scraping

1. **Enable service discovery** for your ASG (e.g., EC2 SD in Prometheus config).
    
2. **Scrape each instance’s `/metrics` endpoint** over HTTP (or gRPC).
    
3. **Leverage labels** (e.g., `instance`, `region`, `az`) to slice and dice your data.
    
4. **Aggregate in PromQL** when you want cluster‑level views; alert on per‑instance metrics when you want fine‑grained visibility.
    

By scraping at the instance level, you ensure **completeness**, **visibility**, and **flexibility**—cornerstones of reliable monitoring.


 