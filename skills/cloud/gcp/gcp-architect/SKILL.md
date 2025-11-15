---
name: gcp-architect
description: Expert in Google Cloud Platform architecture, services, and best practices. Use for GCP infrastructure design, deployment, and cloud-native applications.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# GCP Solutions Architect Expert

## Purpose
Provide expert guidance on Google Cloud Platform architecture, service selection, deployment strategies, and cloud-native application design on GCP.

## When to Use This Skill
- Designing GCP infrastructure
- Selecting appropriate GCP services
- Migrating applications to GCP
- Implementing serverless on GCP
- Setting up CI/CD with Cloud Build
- BigQuery data warehousing
- Kubernetes on GKE
- Cost optimization

## Core GCP Services

### Compute
- **Compute Engine** - VMs
- **Cloud Run** - Serverless containers
- **Cloud Functions** - Serverless functions
- **GKE** - Google Kubernetes Engine
- **App Engine** - PaaS

### Storage
- **Cloud Storage** - Object storage
- **Persistent Disk** - Block storage
- **Filestore** - File storage
- **Cloud SQL** - Managed relational databases
- **Firestore** - NoSQL document database
- **Bigtable** - Wide-column NoSQL
- **Spanner** - Global relational database

### Networking
- **VPC** - Virtual Private Cloud
- **Cloud Load Balancing** - Global load balancing
- **Cloud CDN** - Content delivery
- **Cloud DNS** - Domain name system
- **Cloud Armor** - DDoS protection

### Data & Analytics
- **BigQuery** - Data warehouse
- **Dataflow** - Stream/batch processing
- **Pub/Sub** - Messaging service
- **Data Fusion** - ETL tool
- **Composer** - Workflow orchestration (Airflow)

### Security & Identity
- **IAM** - Identity and Access Management
- **Secret Manager** - Secrets storage
- **Cloud KMS** - Key management
- **Identity Platform** - User authentication

### DevOps & Monitoring
- **Cloud Build** - CI/CD
- **Cloud Monitoring** - Ops metrics
- **Cloud Logging** - Log management
- **Cloud Trace** - Distributed tracing
- **Cloud Profiler** - Performance profiling

## Architecture Patterns

### 1. Serverless Web Application
```
Architecture:
┌──────────────────────────────────────────┐
│   Cloud CDN + Cloud Storage              │
│   (Static Frontend)                      │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Cloud Load Balancer                    │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Cloud Run (Containers)                 │
│   Auto-scaling Microservices             │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Cloud SQL + Firestore                  │
└──────────────────────────────────────────┘

Terraform Configuration:
```hcl
resource "google_cloud_run_service" "api" {
  name     = "api-service"
  location = "us-central1"

  template {
    spec {
      containers {
        image = "gcr.io/project-id/api:latest"

        resources {
          limits = {
            cpu    = "1000m"
            memory = "512Mi"
          }
        }

        env {
          name  = "DATABASE_URL"
          value = google_sql_database_instance.main.connection_name
        }

        env {
          name = "API_KEY"
          value_from {
            secret_key_ref {
              name = google_secret_manager_secret.api_key.secret_id
              key  = "latest"
            }
          }
        }

        ports {
          container_port = 8080
        }
      }

      service_account_name = google_service_account.api.email

      timeout_seconds = 300

      container_concurrency = 80
    }

    metadata {
      annotations = {
        "autoscaling.knative.dev/maxScale" = "100"
        "autoscaling.knative.dev/minScale" = "1"
        "run.googleapis.com/cloudsql-instances" = google_sql_database_instance.main.connection_name
      }
    }
  }

  traffic {
    percent         = 100
    latest_revision = true
  }
}

resource "google_cloud_run_service_iam_member" "public" {
  service  = google_cloud_run_service.api.name
  location = google_cloud_run_service.api.location
  role     = "roles/run.invoker"
  member   = "allUsers"
}

resource "google_sql_database_instance" "main" {
  name             = "main-instance"
  database_version = "POSTGRES_14"
  region           = "us-central1"

  settings {
    tier = "db-f1-micro"

    backup_configuration {
      enabled    = true
      start_time = "03:00"
    }

    ip_configuration {
      ipv4_enabled = false
      private_network = google_compute_network.vpc.id
    }

    database_flags {
      name  = "max_connections"
      value = "100"
    }
  }

  deletion_protection = true
}

resource "google_storage_bucket" "frontend" {
  name          = "my-app-frontend"
  location      = "US"
  force_destroy = false

  uniform_bucket_level_access = true

  website {
    main_page_suffix = "index.html"
    not_found_page   = "404.html"
  }

  cors {
    origin          = ["https://example.com"]
    method          = ["GET", "HEAD"]
    response_header = ["*"]
    max_age_seconds = 3600
  }
}

resource "google_compute_backend_bucket" "cdn" {
  name        = "cdn-backend"
  bucket_name = google_storage_bucket.frontend.name
  enable_cdn  = true

  cdn_policy {
    cache_mode        = "CACHE_ALL_STATIC"
    client_ttl        = 3600
    default_ttl       = 3600
    max_ttl           = 86400
    negative_caching  = true
    serve_while_stale = 86400
  }
}
```

### 2. GKE Microservices
```
Architecture:
┌──────────────────────────────────────────┐
│   Cloud Load Balancer (Ingress)         │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   GKE Cluster                            │
│   ┌──────────┐  ┌──────────┐           │
│   │Service A │  │Service B │           │
│   └──────────┘  └──────────┘           │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Cloud SQL + Memorystore                │
└──────────────────────────────────────────┘

Kubernetes Manifests:
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
        version: v1
    spec:
      serviceAccountName: user-service-sa
      containers:
      - name: user-service
        image: gcr.io/project-id/user-service:v1.2.3
        ports:
        - containerPort: 8080
          protocol: TCP
        env:
        - name: DATABASE_HOST
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: host
        - name: REDIS_HOST
          value: "10.0.0.3:6379"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5

---
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: user-service
  namespace: production
spec:
  selector:
    app: user-service
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: ClusterIP

---
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: user-service-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: user-service
  minReplicas: 3
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80

---
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: main-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: "gce"
    kubernetes.io/ingress.global-static-ip-name: "web-static-ip"
    networking.gke.io/managed-certificates: "web-ssl-cert"
spec:
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /users/*
        pathType: ImplementationSpecific
        backend:
          service:
            name: user-service
            port:
              number: 80
```

### 3. Data Pipeline with BigQuery
```
Architecture:
┌──────────────────────────────────────────┐
│   Data Sources (APIs, Databases, Files) │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Cloud Pub/Sub (Streaming)              │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Dataflow (Processing)                  │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   BigQuery (Data Warehouse)              │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Looker/Data Studio (Visualization)     │
└──────────────────────────────────────────┘

Python Dataflow Pipeline:
```python
import apache_beam as beam
from apache_beam.options.pipeline_options import PipelineOptions
from apache_beam.io.gcp.bigquery import WriteToBigQuery

class TransformData(beam.DoFn):
    def process(self, element):
        import json
        data = json.loads(element)

        # Transform logic
        transformed = {
            'user_id': data['id'],
            'event_type': data['type'],
            'timestamp': data['timestamp'],
            'value': data.get('value', 0)
        }

        yield transformed

def run():
    options = PipelineOptions(
        project='my-project',
        runner='DataflowRunner',
        region='us-central1',
        temp_location='gs://my-bucket/temp',
        staging_location='gs://my-bucket/staging'
    )

    with beam.Pipeline(options=options) as pipeline:
        (
            pipeline
            | 'Read from Pub/Sub' >> beam.io.ReadFromPubSub(
                subscription='projects/my-project/subscriptions/my-sub'
              )
            | 'Transform' >> beam.ParDo(TransformData())
            | 'Write to BigQuery' >> WriteToBigQuery(
                'my-project:dataset.table',
                schema='user_id:STRING,event_type:STRING,timestamp:TIMESTAMP,value:FLOAT',
                create_disposition=beam.io.BigQueryDisposition.CREATE_IF_NEEDED,
                write_disposition=beam.io.BigQueryDisposition.WRITE_APPEND
              )
        )

if __name__ == '__main__':
    run()
```

## Security Best Practices

### 1. IAM Roles
```hcl
resource "google_project_iam_custom_role" "app_role" {
  role_id     = "appRole"
  title       = "Application Role"
  description = "Custom role for application"
  permissions = [
    "storage.objects.get",
    "storage.objects.create",
    "cloudsql.instances.connect"
  ]
}

resource "google_service_account" "app" {
  account_id   = "app-service-account"
  display_name = "Application Service Account"
}

resource "google_project_iam_member" "app_binding" {
  project = var.project_id
  role    = google_project_iam_custom_role.app_role.id
  member  = "serviceAccount:${google_service_account.app.email}"
}
```

### 2. VPC Security
- Private Google Access for API access without internet
- Cloud NAT for outbound internet
- Firewall rules for network security
- VPC Service Controls for data exfiltration protection

### 3. Secret Management
```bash
# Create secret
gcloud secrets create api-key --data-file=./key.txt

# Access in Cloud Run
gcloud run deploy api \
  --image gcr.io/project/api \
  --set-secrets=API_KEY=api-key:latest
```

## Cost Optimization

1. **Committed Use Discounts** - Up to 57% savings
2. **Sustained Use Discounts** - Automatic for Compute Engine
3. **Preemptible VMs** - Up to 80% savings
4. **Cloud Storage Classes** - Standard, Nearline, Coldline, Archive
5. **BigQuery** - Use clustering and partitioning

## Monitoring

```python
from google.cloud import monitoring_v3

client = monitoring_v3.MetricServiceClient()
project_name = f"projects/{project_id}"

series = monitoring_v3.TimeSeries()
series.metric.type = "custom.googleapis.com/my_metric"
series.resource.type = "gce_instance"

point = monitoring_v3.Point()
point.value.double_value = 3.14
now = time.time()
point.interval.end_time.seconds = int(now)

series.points = [point]
client.create_time_series(name=project_name, time_series=[series])
```

This skill ensures well-architected, secure GCP solutions optimized for performance and cost.
