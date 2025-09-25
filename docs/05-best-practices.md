# SRE Agent - Best Practices Guide

## Document Information
- **Version**: 1.0
- **Date**: January 2025
- **Author**: SRE Team
- **Status**: Draft

## 1. Executive Summary

This document outlines best practices for developing, deploying, and maintaining the SRE Agent system. These practices ensure high quality, security, performance, and maintainability while following industry standards and enterprise requirements.

## 2. Development Best Practices

### 2.1 Code Quality Standards

#### 2.1.1 Python Code Standards
```python
# Use type hints for all functions
def process_alert(alert_data: AlertData) -> AnalysisResult:
    """Process alert data and return analysis result.
    
    Args:
        alert_data: Alert data from Slack webhook
        Returns:
            AnalysisResult: Structured analysis result
    """
    pass

# Use dataclasses for data structures
@dataclass
class AlertData:
    """Alert data structure."""
    alert_id: str
    service_name: str
    severity: AlertSeverity
    timestamp: datetime
    message: str

# Use enums for constants
class AlertSeverity(Enum):
    """Alert severity levels."""
    CRITICAL = "critical"
    HIGH = "high"
    MEDIUM = "medium"
    LOW = "low"
```

#### 2.1.2 Code Organization
```
sre_agent/
├── agents/                 # Specialized agents
│   ├── network_agent.py
│   ├── database_agent.py
│   └── infrastructure_agent.py
├── mcp_servers/           # MCP server implementations
│   ├── signoz_server.py
│   ├── kubernetes_server.py
│   └── mongodb_server.py
├── core/                  # Core functionality
│   ├── router.py
│   ├── analysis_engine.py
│   └── response_generator.py
├── utils/                 # Utility functions
│   ├── logging.py
│   ├── validation.py
│   └── caching.py
├── config/                # Configuration files
│   ├── agents.yaml
│   └── settings.yaml
└── tests/                 # Test files
    ├── unit/
    ├── integration/
    └── e2e/
```

#### 2.1.3 Error Handling
```python
import logging
from typing import Optional
from exceptions import SREAgentError, DataSourceError

logger = logging.getLogger(__name__)

class AlertProcessor:
    """Alert processor with comprehensive error handling."""
    
    def process_alert(self, alert_data: AlertData) -> Optional[AnalysisResult]:
        """Process alert with proper error handling."""
        try:
            # Validate input
            self._validate_alert_data(alert_data)
            
            # Process alert
            result = self._analyze_alert(alert_data)
            
            # Log success
            logger.info(f"Successfully processed alert {alert_data.alert_id}")
            return result
            
        except DataSourceError as e:
            logger.error(f"DataSource error processing alert {alert_data.alert_id}: {e}")
            # Implement fallback mechanism
            return self._fallback_analysis(alert_data)
            
        except SREAgentError as e:
            logger.error(f"SRE Agent error processing alert {alert_data.alert_id}: {e}")
            # Return partial result if possible
            return self._partial_analysis(alert_data)
            
        except Exception as e:
            logger.critical(f"Unexpected error processing alert {alert_data.alert_id}: {e}")
            # Notify administrators
            self._notify_administrators(alert_data, e)
            return None
    
    def _validate_alert_data(self, alert_data: AlertData) -> None:
        """Validate alert data."""
        if not alert_data.alert_id:
            raise ValueError("Alert ID is required")
        if not alert_data.service_name:
            raise ValueError("Service name is required")
        # Add more validation as needed
```

### 2.2 Testing Best Practices

#### 2.2.1 Unit Testing
```python
import pytest
from unittest.mock import Mock, patch
from agents.network_agent import NetworkAgent
from models import AlertData, AlertSeverity

class TestNetworkAgent:
    """Test cases for NetworkAgent."""
    
    def setup_method(self):
        """Set up test fixtures."""
        self.agent = NetworkAgent()
        self.sample_alert = AlertData(
            alert_id="test-123",
            service_name="web-service",
            severity=AlertSeverity.HIGH,
            timestamp=datetime.now(),
            message="SSL connection failed"
        )
    
    def test_analyze_ssl_issue(self):
        """Test SSL issue analysis."""
        # Arrange
        with patch('mcp_servers.signoz_server.SignozServer.query_logs') as mock_query:
            mock_query.return_value = {
                "logs": [{"message": "SSL handshake failed", "timestamp": "2025-01-01T10:00:00Z"}]
            }
            
            # Act
            result = self.agent.analyze_ssl_issue(self.sample_alert)
            
            # Assert
            assert result is not None
            assert result.root_cause == "SSL handshake failure"
            assert result.severity == AlertSeverity.HIGH
            mock_query.assert_called_once()
    
    def test_analyze_ssl_issue_with_invalid_data(self):
        """Test SSL issue analysis with invalid data."""
        # Arrange
        invalid_alert = AlertData(
            alert_id="",
            service_name="",
            severity=AlertSeverity.HIGH,
            timestamp=datetime.now(),
            message=""
        )
        
        # Act & Assert
        with pytest.raises(ValueError):
            self.agent.analyze_ssl_issue(invalid_alert)
    
    @pytest.mark.parametrize("severity,expected_priority", [
        (AlertSeverity.CRITICAL, "P0"),
        (AlertSeverity.HIGH, "P1"),
        (AlertSeverity.MEDIUM, "P2"),
        (AlertSeverity.LOW, "P3"),
    ])
    def test_severity_to_priority_mapping(self, severity, expected_priority):
        """Test severity to priority mapping."""
        result = self.agent._map_severity_to_priority(severity)
        assert result == expected_priority
```

#### 2.2.2 Integration Testing
```python
import pytest
from fastapi.testclient import TestClient
from main import app

class TestSREAgentIntegration:
    """Integration tests for SRE Agent."""
    
    def setup_method(self):
        """Set up test client."""
        self.client = TestClient(app)
    
    def test_alert_processing_endpoint(self):
        """Test alert processing endpoint."""
        # Arrange
        alert_payload = {
            "alert_id": "test-123",
            "service_name": "web-service",
            "severity": "high",
            "message": "SSL connection failed"
        }
        
        # Act
        response = self.client.post("/api/v1/alerts", json=alert_payload)
        
        # Assert
        assert response.status_code == 200
        data = response.json()
        assert data["status"] == "success"
        assert data["analysis_id"] is not None
    
    def test_slack_webhook_integration(self):
        """Test Slack webhook integration."""
        # Arrange
        slack_payload = {
            "text": "Alert: SSL connection failed",
            "channel": "alerts",
            "user": "test-user"
        }
        
        # Act
        response = self.client.post("/api/v1/slack/webhook", json=slack_payload)
        
        # Assert
        assert response.status_code == 200
        data = response.json()
        assert data["status"] == "received"
```

#### 2.2.3 End-to-End Testing
```python
import pytest
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

class TestSREAgentE2E:
    """End-to-end tests for SRE Agent."""
    
    def setup_method(self):
        """Set up browser driver."""
        self.driver = webdriver.Chrome()
        self.wait = WebDriverWait(self.driver, 10)
    
    def teardown_method(self):
        """Clean up browser driver."""
        self.driver.quit()
    
    def test_slack_integration_flow(self):
        """Test complete Slack integration flow."""
        # Navigate to Slack
        self.driver.get("https://slack.com")
        
        # Login (simplified for testing)
        # ... login steps ...
        
        # Send test alert
        # ... send alert steps ...
        
        # Verify agent response
        # ... verify response steps ...
        
        # Assert response quality
        response_element = self.wait.until(
            EC.presence_of_element_located((By.CLASS_NAME, "agent-response"))
        )
        assert "Root Cause Analysis" in response_element.text
```

### 2.3 Security Best Practices

#### 2.3.1 Authentication and Authorization
```python
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from jose import JWTError, jwt
from passlib.context import CryptContext
from typing import Optional

# Security configuration
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
security = HTTPBearer()

class SecurityManager:
    """Security manager for authentication and authorization."""
    
    def __init__(self, secret_key: str, algorithm: str = "HS256"):
        self.secret_key = secret_key
        self.algorithm = algorithm
    
    def verify_token(self, credentials: HTTPAuthorizationCredentials = Depends(security)):
        """Verify JWT token."""
        try:
            payload = jwt.decode(credentials.credentials, self.secret_key, algorithms=[self.algorithm])
            username: str = payload.get("sub")
            if username is None:
                raise HTTPException(
                    status_code=status.HTTP_401_UNAUTHORIZED,
                    detail="Invalid authentication credentials",
                    headers={"WWW-Authenticate": "Bearer"},
                )
            return username
        except JWTError:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Invalid authentication credentials",
                headers={"WWW-Authenticate": "Bearer"},
            )
    
    def check_permissions(self, user: str, resource: str, action: str) -> bool:
        """Check user permissions for resource and action."""
        # Implement RBAC logic
        user_roles = self.get_user_roles(user)
        required_permissions = self.get_required_permissions(resource, action)
        
        for role in user_roles:
            if self.role_has_permissions(role, required_permissions):
                return True
        return False
```

#### 2.3.2 Data Protection
```python
from cryptography.fernet import Fernet
from typing import Any, Dict
import json

class DataProtection:
    """Data protection utilities."""
    
    def __init__(self, encryption_key: bytes):
        self.cipher = Fernet(encryption_key)
    
    def encrypt_sensitive_data(self, data: Dict[str, Any]) -> str:
        """Encrypt sensitive data."""
        # Remove sensitive fields
        sensitive_fields = ["password", "api_key", "secret", "token"]
        for field in sensitive_fields:
            if field in data:
                data[field] = "***REDACTED***"
        
        # Encrypt the data
        json_data = json.dumps(data)
        encrypted_data = self.cipher.encrypt(json_data.encode())
        return encrypted_data.decode()
    
    def decrypt_sensitive_data(self, encrypted_data: str) -> Dict[str, Any]:
        """Decrypt sensitive data."""
        decrypted_data = self.cipher.decrypt(encrypted_data.encode())
        return json.loads(decrypted_data.decode())
    
    def sanitize_log_data(self, data: Dict[str, Any]) -> Dict[str, Any]:
        """Sanitize data for logging."""
        sensitive_fields = ["password", "api_key", "secret", "token", "ssn", "credit_card"]
        sanitized_data = data.copy()
        
        for field in sensitive_fields:
            if field in sanitized_data:
                sanitized_data[field] = "***REDACTED***"
        
        return sanitized_data
```

### 2.4 Performance Best Practices

#### 2.4.1 Caching Strategy
```python
import redis
from typing import Any, Optional
import json
import hashlib

class CacheManager:
    """Redis-based cache manager."""
    
    def __init__(self, redis_url: str, default_ttl: int = 3600):
        self.redis_client = redis.from_url(redis_url)
        self.default_ttl = default_ttl
    
    def get_cache_key(self, prefix: str, **kwargs) -> str:
        """Generate cache key from parameters."""
        key_data = f"{prefix}:{json.dumps(kwargs, sort_keys=True)}"
        return hashlib.md5(key_data.encode()).hexdigest()
    
    def get(self, key: str) -> Optional[Any]:
        """Get value from cache."""
        try:
            cached_data = self.redis_client.get(key)
            if cached_data:
                return json.loads(cached_data)
        except Exception as e:
            logger.warning(f"Cache get error: {e}")
        return None
    
    def set(self, key: str, value: Any, ttl: Optional[int] = None) -> bool:
        """Set value in cache."""
        try:
            ttl = ttl or self.default_ttl
            serialized_value = json.dumps(value)
            return self.redis_client.setex(key, ttl, serialized_value)
        except Exception as e:
            logger.warning(f"Cache set error: {e}")
            return False
    
    def invalidate_pattern(self, pattern: str) -> int:
        """Invalidate cache keys matching pattern."""
        try:
            keys = self.redis_client.keys(pattern)
            if keys:
                return self.redis_client.delete(*keys)
        except Exception as e:
            logger.warning(f"Cache invalidation error: {e}")
        return 0
```

#### 2.4.2 Async Processing
```python
import asyncio
from typing import List, Dict, Any
from concurrent.futures import ThreadPoolExecutor

class AsyncProcessor:
    """Async processor for handling multiple requests."""
    
    def __init__(self, max_workers: int = 10):
        self.executor = ThreadPoolExecutor(max_workers=max_workers)
    
    async def process_alerts_batch(self, alerts: List[AlertData]) -> List[AnalysisResult]:
        """Process multiple alerts concurrently."""
        tasks = []
        for alert in alerts:
            task = asyncio.create_task(self._process_single_alert(alert))
            tasks.append(task)
        
        results = await asyncio.gather(*tasks, return_exceptions=True)
        
        # Filter out exceptions and return valid results
        valid_results = [r for r in results if not isinstance(r, Exception)]
        return valid_results
    
    async def _process_single_alert(self, alert: AlertData) -> AnalysisResult:
        """Process a single alert asynchronously."""
        loop = asyncio.get_event_loop()
        return await loop.run_in_executor(
            self.executor, 
            self._sync_process_alert, 
            alert
        )
    
    def _sync_process_alert(self, alert: AlertData) -> AnalysisResult:
        """Synchronous alert processing."""
        # Implement alert processing logic
        pass
```

## 3. Deployment Best Practices

### 3.1 Containerization

#### 3.1.1 Dockerfile Best Practices
```dockerfile
# Use multi-stage build for smaller image size
FROM python:3.11-slim as builder

# Set working directory
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    g++ \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements and install Python dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# Production stage
FROM python:3.11-slim

# Create non-root user
RUN groupadd -r appuser && useradd -r -g appuser appuser

# Set working directory
WORKDIR /app

# Copy Python packages from builder stage
COPY --from=builder /root/.local /home/appuser/.local

# Copy application code
COPY --chown=appuser:appuser . .

# Switch to non-root user
USER appuser

# Set PATH to include user packages
ENV PATH=/home/appuser/.local/bin:$PATH

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD python -c "import requests; requests.get('http://localhost:8000/health')"

# Expose port
EXPOSE 8000

# Run application
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

#### 3.1.2 Docker Compose for Development
```yaml
version: '3.8'

services:
  sre-agent:
    build: .
    ports:
      - "8000:8000"
    environment:
      - REDIS_URL=redis://redis:6379
      - MONGODB_URL=mongodb://mongodb:27017
    depends_on:
      - redis
      - mongodb
    volumes:
      - ./config:/app/config
      - ./logs:/app/logs

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  mongodb:
    image: mongo:7
    ports:
      - "27017:27017"
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=password
    volumes:
      - mongodb_data:/data/db

volumes:
  redis_data:
  mongodb_data:
```

### 3.2 Kubernetes Deployment

#### 3.2.1 Deployment Manifest
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sre-agent
  labels:
    app: sre-agent
spec:
  replicas: 3
  selector:
    matchLabels:
      app: sre-agent
  template:
    metadata:
      labels:
        app: sre-agent
    spec:
      containers:
      - name: sre-agent
        image: sre-agent:latest
        ports:
        - containerPort: 8000
        env:
        - name: REDIS_URL
          valueFrom:
            configMapKeyRef:
              name: sre-agent-config
              key: redis-url
        - name: OPENAI_API_KEY
          valueFrom:
            secretKeyRef:
              name: sre-agent-secrets
              key: openai-api-key
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8000
          initialDelaySeconds: 5
          periodSeconds: 5
        volumeMounts:
        - name: config-volume
          mountPath: /app/config
      volumes:
      - name: config-volume
        configMap:
          name: sre-agent-config
```

#### 3.2.2 Service and Ingress
```yaml
apiVersion: v1
kind: Service
metadata:
  name: sre-agent-service
spec:
  selector:
    app: sre-agent
  ports:
  - port: 80
    targetPort: 8000
  type: ClusterIP

---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: sre-agent-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
  - hosts:
    - sre-agent.company.com
    secretName: sre-agent-tls
  rules:
  - host: sre-agent.company.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: sre-agent-service
            port:
              number: 80
```

### 3.3 CI/CD Pipeline

#### 3.3.1 GitHub Actions Workflow
```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        pip install -r requirements-dev.txt
    
    - name: Run linting
      run: |
        flake8 .
        black --check .
        mypy .
    
    - name: Run tests
      run: |
        pytest --cov=. --cov-report=xml
    
    - name: Upload coverage
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage.xml

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Log in to Container Registry
      uses: docker/login-action@v3
      with:
        registry: ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
    
    - name: Extract metadata
      id: meta
      uses: docker/metadata-action@v5
      with:
        images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
        tags: |
          type=ref,event=branch
          type=ref,event=pr
          type=sha,prefix={{branch}}-
    
    - name: Build and push Docker image
      uses: docker/build-push-action@v5
      with:
        context: .
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
    - uses: actions/checkout@v4
    
    - name: Configure kubectl
      uses: azure/k8s-set-context@v3
      with:
        method: kubeconfig
        kubeconfig: ${{ secrets.KUBE_CONFIG }}
    
    - name: Deploy to Kubernetes
      run: |
        kubectl apply -f k8s/
        kubectl rollout status deployment/sre-agent
```

## 4. Monitoring and Observability Best Practices

### 4.1 Logging Standards

#### 4.1.1 Structured Logging
```python
import logging
import json
from datetime import datetime
from typing import Dict, Any

class StructuredLogger:
    """Structured logger for SRE Agent."""
    
    def __init__(self, name: str):
        self.logger = logging.getLogger(name)
        self.logger.setLevel(logging.INFO)
        
        # Create formatter
        formatter = logging.Formatter(
            '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
        )
        
        # Create handler
        handler = logging.StreamHandler()
        handler.setFormatter(formatter)
        self.logger.addHandler(handler)
    
    def log_alert_processing(self, alert_id: str, service_name: str, 
                           duration: float, status: str, **kwargs):
        """Log alert processing event."""
        log_data = {
            "event_type": "alert_processing",
            "alert_id": alert_id,
            "service_name": service_name,
            "duration_ms": duration * 1000,
            "status": status,
            "timestamp": datetime.utcnow().isoformat(),
            **kwargs
        }
        self.logger.info(json.dumps(log_data))
    
    def log_error(self, error_type: str, error_message: str, 
                 context: Dict[str, Any] = None):
        """Log error event."""
        log_data = {
            "event_type": "error",
            "error_type": error_type,
            "error_message": error_message,
            "timestamp": datetime.utcnow().isoformat(),
            "context": context or {}
        }
        self.logger.error(json.dumps(log_data))
    
    def log_performance(self, operation: str, duration: float, 
                       metrics: Dict[str, Any] = None):
        """Log performance metrics."""
        log_data = {
            "event_type": "performance",
            "operation": operation,
            "duration_ms": duration * 1000,
            "timestamp": datetime.utcnow().isoformat(),
            "metrics": metrics or {}
        }
        self.logger.info(json.dumps(log_data))
```

#### 4.1.2 Log Aggregation
```yaml
# Fluentd configuration for log aggregation
apiVersion: v1
kind: ConfigMap
metadata:
  name: fluentd-config
data:
  fluent.conf: |
    <source>
      @type tail
      path /var/log/sre-agent/*.log
      pos_file /var/log/fluentd/sre-agent.log.pos
      tag sre-agent
      format json
    </source>
    
    <match sre-agent>
      @type elasticsearch
      host elasticsearch.logging.svc.cluster.local
      port 9200
      index_name sre-agent-logs
      type_name _doc
    </match>
```

### 4.2 Metrics Collection

#### 4.2.1 Prometheus Metrics
```python
from prometheus_client import Counter, Histogram, Gauge, start_http_server
import time

# Define metrics
ALERT_PROCESSING_TOTAL = Counter(
    'sre_agent_alerts_processed_total',
    'Total number of alerts processed',
    ['status', 'agent_type']
)

ALERT_PROCESSING_DURATION = Histogram(
    'sre_agent_alert_processing_duration_seconds',
    'Time spent processing alerts',
    ['agent_type']
)

ACTIVE_ALERTS = Gauge(
    'sre_agent_active_alerts',
    'Number of active alerts being processed'
)

class MetricsCollector:
    """Metrics collector for SRE Agent."""
    
    def __init__(self, port: int = 8001):
        self.port = port
        start_http_server(port)
    
    def record_alert_processing(self, agent_type: str, status: str, duration: float):
        """Record alert processing metrics."""
        ALERT_PROCESSING_TOTAL.labels(
            status=status, 
            agent_type=agent_type
        ).inc()
        
        ALERT_PROCESSING_DURATION.labels(
            agent_type=agent_type
        ).observe(duration)
    
    def set_active_alerts(self, count: int):
        """Set active alerts count."""
        ACTIVE_ALERTS.set(count)
```

#### 4.2.2 Custom Dashboards
```yaml
# Grafana dashboard configuration
apiVersion: v1
kind: ConfigMap
metadata:
  name: sre-agent-dashboard
data:
  dashboard.json: |
    {
      "dashboard": {
        "title": "SRE Agent Dashboard",
        "panels": [
          {
            "title": "Alerts Processed",
            "type": "graph",
            "targets": [
              {
                "expr": "rate(sre_agent_alerts_processed_total[5m])",
                "legendFormat": "{{agent_type}} - {{status}}"
              }
            ]
          },
          {
            "title": "Processing Duration",
            "type": "graph",
            "targets": [
              {
                "expr": "histogram_quantile(0.95, rate(sre_agent_alert_processing_duration_seconds_bucket[5m]))",
                "legendFormat": "95th percentile"
              }
            ]
          }
        ]
      }
    }
```

### 4.3 Health Checks

#### 4.3.1 Application Health Checks
```python
from fastapi import FastAPI, status
from fastapi.responses import JSONResponse
import asyncio

app = FastAPI()

class HealthChecker:
    """Health checker for SRE Agent."""
    
    def __init__(self):
        self.checks = {
            "database": self._check_database,
            "redis": self._check_redis,
            "openai": self._check_openai,
            "signoz": self._check_signoz
        }
    
    async def check_health(self) -> Dict[str, Any]:
        """Check health of all components."""
        results = {}
        overall_healthy = True
        
        for name, check_func in self.checks.items():
            try:
                result = await check_func()
                results[name] = {
                    "status": "healthy" if result else "unhealthy",
                    "details": result
                }
                if not result:
                    overall_healthy = False
            except Exception as e:
                results[name] = {
                    "status": "error",
                    "error": str(e)
                }
                overall_healthy = False
        
        return {
            "overall_status": "healthy" if overall_healthy else "unhealthy",
            "checks": results,
            "timestamp": datetime.utcnow().isoformat()
        }
    
    async def _check_database(self) -> bool:
        """Check database connectivity."""
        # Implement database health check
        return True
    
    async def _check_redis(self) -> bool:
        """Check Redis connectivity."""
        # Implement Redis health check
        return True
    
    async def _check_openai(self) -> bool:
        """Check OpenAI API connectivity."""
        # Implement OpenAI health check
        return True
    
    async def _check_signoz(self) -> bool:
        """Check Signoz connectivity."""
        # Implement Signoz health check
        return True

health_checker = HealthChecker()

@app.get("/health")
async def health_check():
    """Health check endpoint."""
    health_status = await health_checker.check_health()
    
    if health_status["overall_status"] == "healthy":
        return JSONResponse(
            status_code=status.HTTP_200_OK,
            content=health_status
        )
    else:
        return JSONResponse(
            status_code=status.HTTP_503_SERVICE_UNAVAILABLE,
            content=health_status
        )

@app.get("/ready")
async def readiness_check():
    """Readiness check endpoint."""
    # Check if application is ready to serve requests
    return {"status": "ready"}
```

## 5. Security Best Practices

### 5.1 Secrets Management

#### 5.1.1 Kubernetes Secrets
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: sre-agent-secrets
type: Opaque
data:
  openai-api-key: <base64-encoded-key>
  slack-bot-token: <base64-encoded-token>
  mongodb-connection-string: <base64-encoded-connection>
  redis-password: <base64-encoded-password>
```

#### 5.1.2 Azure Key Vault Integration
```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

class SecretManager:
    """Secret manager using Azure Key Vault."""
    
    def __init__(self, vault_url: str):
        credential = DefaultAzureCredential()
        self.client = SecretClient(vault_url=vault_url, credential=credential)
    
    def get_secret(self, secret_name: str) -> str:
        """Get secret from Key Vault."""
        try:
            secret = self.client.get_secret(secret_name)
            return secret.value
        except Exception as e:
            logger.error(f"Failed to get secret {secret_name}: {e}")
            raise
    
    def set_secret(self, secret_name: str, secret_value: str):
        """Set secret in Key Vault."""
        try:
            self.client.set_secret(secret_name, secret_value)
        except Exception as e:
            logger.error(f"Failed to set secret {secret_name}: {e}")
            raise
```

### 5.2 Network Security

#### 5.2.1 Network Policies
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: sre-agent-network-policy
spec:
  podSelector:
    matchLabels:
      app: sre-agent
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: monitoring
    ports:
    - protocol: TCP
      port: 8000
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: monitoring
    ports:
    - protocol: TCP
      port: 9090
  - to: []
    ports:
    - protocol: TCP
      port: 443
    - protocol: TCP
      port: 80
```

### 5.3 Data Protection

#### 5.3.1 Data Encryption
```python
from cryptography.fernet import Fernet
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
import base64
import os

class DataEncryption:
    """Data encryption utilities."""
    
    def __init__(self, password: str, salt: bytes = None):
        self.salt = salt or os.urandom(16)
        kdf = PBKDF2HMAC(
            algorithm=hashes.SHA256(),
            length=32,
            salt=self.salt,
            iterations=100000,
        )
        key = base64.urlsafe_b64encode(kdf.derive(password.encode()))
        self.cipher = Fernet(key)
    
    def encrypt(self, data: str) -> str:
        """Encrypt data."""
        return self.cipher.encrypt(data.encode()).decode()
    
    def decrypt(self, encrypted_data: str) -> str:
        """Decrypt data."""
        return self.cipher.decrypt(encrypted_data.encode()).decode()
```

## 6. Maintenance and Operations Best Practices

### 6.1 Backup and Recovery

#### 6.1.1 Database Backup
```bash
#!/bin/bash
# Database backup script

BACKUP_DIR="/backups/mongodb"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="sre_agent_backup_${DATE}.gz"

# Create backup
mongodump --uri="$MONGODB_URI" --gzip --archive="$BACKUP_DIR/$BACKUP_FILE"

# Upload to cloud storage
aws s3 cp "$BACKUP_DIR/$BACKUP_FILE" "s3://sre-agent-backups/"

# Clean up old backups (keep last 30 days)
find "$BACKUP_DIR" -name "*.gz" -mtime +30 -delete
```

#### 6.1.2 Configuration Backup
```bash
#!/bin/bash
# Configuration backup script

CONFIG_DIR="/app/config"
BACKUP_DIR="/backups/config"
DATE=$(date +%Y%m%d_%H%M%S)

# Create backup
tar -czf "$BACKUP_DIR/config_backup_${DATE}.tar.gz" -C "$CONFIG_DIR" .

# Upload to cloud storage
aws s3 cp "$BACKUP_DIR/config_backup_${DATE}.tar.gz" "s3://sre-agent-backups/config/"
```

### 6.2 Monitoring and Alerting

#### 6.2.1 Alert Rules
```yaml
# Prometheus alert rules
groups:
- name: sre-agent
  rules:
  - alert: SREAgentDown
    expr: up{job="sre-agent"} == 0
    for: 1m
    labels:
      severity: critical
    annotations:
      summary: "SRE Agent is down"
      description: "SRE Agent has been down for more than 1 minute"
  
  - alert: HighErrorRate
    expr: rate(sre_agent_errors_total[5m]) > 0.1
    for: 2m
    labels:
      severity: warning
    annotations:
      summary: "High error rate in SRE Agent"
      description: "Error rate is {{ $value }} errors per second"
  
  - alert: HighProcessingTime
    expr: histogram_quantile(0.95, rate(sre_agent_alert_processing_duration_seconds_bucket[5m])) > 300
    for: 5m
    labels:
      severity: warning
    annotations:
      summary: "High processing time in SRE Agent"
      description: "95th percentile processing time is {{ $value }} seconds"
```

### 6.3 Documentation and Training

#### 6.3.1 API Documentation
```python
from fastapi import FastAPI
from fastapi.openapi.utils import get_openapi

app = FastAPI(
    title="SRE Agent API",
    description="API for SRE Agent system",
    version="1.0.0",
    docs_url="/docs",
    redoc_url="/redoc"
)

def custom_openapi():
    if app.openapi_schema:
        return app.openapi_schema
    openapi_schema = get_openapi(
        title="SRE Agent API",
        version="1.0.0",
        description="API for SRE Agent system",
        routes=app.routes,
    )
    app.openapi_schema = openapi_schema
    return app.openapi_schema

app.openapi = custom_openapi
```

## 7. Conclusion

These best practices provide a comprehensive framework for developing, deploying, and maintaining the SRE Agent system. By following these practices, teams can ensure high quality, security, performance, and maintainability while meeting enterprise requirements and industry standards.

The practices cover all aspects of the system lifecycle, from development and testing to deployment and operations, ensuring that the SRE Agent can operate reliably in a production environment while providing maximum value to users.
