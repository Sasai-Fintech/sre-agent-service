# SRE Agent - External References & MCP Definitions

## Document Information
- **Version**: 1.0
- **Date**: January 2025
- **Author**: SRE Team
- **Status**: Draft

## 1. Executive Summary

This document provides comprehensive external references, MCP (Model Context Protocol) definitions, and integration guides for the SRE Agent project. It serves as a technical reference for developers and operators working with the system.

## 2. Core Framework References

### 2.1 Agno AgentOS
- **Official Documentation**: [https://www.agno.com/docs](https://www.agno.com/docs)
- **GitHub Repository**: [https://github.com/agno-ai/agno](https://github.com/agno-ai/agno)
- **Performance Benchmarks**: 3μs agent instantiation, 6,656 bytes memory usage
- **MCP Integration**: Native MCP server discovery and tool integration
- **Cloud Native**: Designed for Kubernetes and cloud deployment

### 2.2 Unpage Patterns
- **Official Documentation**: [https://docs.unpage.ai](https://docs.unpage.ai)
- **GitHub Repository**: [https://github.com/unpage-ai/unpage](https://github.com/unpage-ai/unpage)
- **YAML Configuration**: Clean, maintainable agent configuration
- **Router Pattern**: Intelligent agent routing based on alert types
- **Specialized Agents**: Focused agents for different problem domains

### 2.3 Model Context Protocol (MCP)
- **Official Specification**: [https://modelcontextprotocol.io](https://modelcontextprotocol.io)
- **GitHub Repository**: [https://github.com/modelcontextprotocol](https://github.com/modelcontextprotocol)
- **Tool Discovery**: Automatic discovery of available tools
- **Security**: Scoped access to tools and data
- **Extensibility**: Easy to add new data sources

## 3. Observability Platform References

### 3.1 Signoz
- **Official Documentation**: [https://signoz.io/docs](https://signoz.io/docs)
- **GitHub Repository**: [https://github.com/SigNoz/signoz](https://github.com/SigNoz/signoz)
- **API Documentation**: [https://signoz.io/docs/api](https://signoz.io/docs/api)
- **ClickHouse Integration**: [https://clickhouse.com/docs](https://clickhouse.com/docs)
- **OpenTelemetry Integration**: [https://opentelemetry.io/docs](https://opentelemetry.io/docs)

### 3.2 ClickHouse
- **Official Documentation**: [https://clickhouse.com/docs](https://clickhouse.com/docs)
- **Python Client**: [https://clickhouse.com/docs/en/interfaces/python](https://clickhouse.com/docs/en/interfaces/python)
- **HTTP Interface**: [https://clickhouse.com/docs/en/interfaces/http](https://clickhouse.com/docs/en/interfaces/http)
- **SQL Reference**: [https://clickhouse.com/docs/en/sql-reference](https://clickhouse.com/docs/en/sql-reference)

### 3.3 OpenTelemetry
- **Official Documentation**: [https://opentelemetry.io/docs](https://opentelemetry.io/docs)
- **Python SDK**: [https://opentelemetry.io/docs/languages/python](https://opentelemetry.io/docs/languages/python)
- **Kubernetes Operator**: [https://opentelemetry.io/docs/kubernetes](https://opentelemetry.io/docs/kubernetes)
- **Collector Configuration**: [https://opentelemetry.io/docs/collector](https://opentelemetry.io/docs/collector)

## 4. Database References

### 4.1 MongoDB Atlas
- **Official Documentation**: [https://www.mongodb.com/docs/atlas](https://www.mongodb.com/docs/atlas)
- **Python Driver**: [https://www.mongodb.com/docs/drivers/python](https://www.mongodb.com/docs/drivers/python)
- **Atlas API**: [https://www.mongodb.com/docs/atlas/api](https://www.mongodb.com/docs/atlas/api)
- **Performance Insights**: [https://www.mongodb.com/docs/atlas/performance-insights](https://www.mongodb.com/docs/atlas/performance-insights)

### 4.2 Redis
- **Official Documentation**: [https://redis.io/docs](https://redis.io/docs)
- **Python Client**: [https://redis-py.readthedocs.io](https://redis-py.readthedocs.io)
- **Kubernetes Deployment**: [https://redis.io/docs/kubernetes](https://redis.io/docs/kubernetes)
- **Caching Patterns**: [https://redis.io/docs/manual/patterns](https://redis.io/docs/manual/patterns)

## 5. Cloud Platform References

### 5.1 Azure Kubernetes Service (AKS)
- **Official Documentation**: [https://docs.microsoft.com/en-us/azure/aks](https://docs.microsoft.com/en-us/azure/aks)
- **Kubernetes Documentation**: [https://kubernetes.io/docs](https://kubernetes.io/docs)
- **AKS Best Practices**: [https://docs.microsoft.com/en-us/azure/aks/best-practices](https://docs.microsoft.com/en-us/azure/aks/best-practices)
- **Security Hardening**: [https://docs.microsoft.com/en-us/azure/aks/security-hardened-vm-host](https://docs.microsoft.com/en-us/azure/aks/security-hardened-vm-host)

### 5.2 Azure OpenAI
- **Official Documentation**: [https://docs.microsoft.com/en-us/azure/cognitive-services/openai](https://docs.microsoft.com/en-us/azure/cognitive-services/openai)
- **API Reference**: [https://docs.microsoft.com/en-us/azure/cognitive-services/openai/reference](https://docs.microsoft.com/en-us/azure/cognitive-services/openai/reference)
- **Python SDK**: [https://docs.microsoft.com/en-us/azure/cognitive-services/openai/quickstart](https://docs.microsoft.com/en-us/azure/cognitive-services/openai/quickstart)
- **Best Practices**: [https://docs.microsoft.com/en-us/azure/cognitive-services/openai/concepts/best-practices](https://docs.microsoft.com/en-us/azure/cognitive-services/openai/concepts/best-practices)

## 6. Communication Platform References

### 6.1 Slack API
- **Official Documentation**: [https://api.slack.com](https://api.slack.com)
- **Python SDK**: [https://slack.dev/python-slack-sdk](https://slack.dev/python-slack-sdk)
- **Webhook Guide**: [https://api.slack.com/messaging/webhooks](https://api.slack.com/messaging/webhooks)
- **Slash Commands**: [https://api.slack.com/interactivity/slash-commands](https://api.slack.com/interactivity/slash-commands)
- **Block Kit**: [https://api.slack.com/block-kit](https://api.slack.com/block-kit)

## 7. MCP Server Definitions

### 7.1 Signoz MCP Server

#### 7.1.1 Server Configuration
```yaml
# signoz-mcp-server.yaml
name: "signoz-mcp-server"
description: "MCP server for Signoz observability data"
version: "1.0.0"
endpoint: "https://signoz.company.com/api/v1"
authentication:
  type: "bearer"
  token: "${SIGNOZ_API_TOKEN}"
```

#### 7.1.2 Available Tools
```python
# Signoz MCP Server Tools
SIGNOZ_TOOLS = {
    "query_logs": {
        "description": "Query logs from Signoz",
        "parameters": {
            "service": {"type": "string", "required": True},
            "time_range": {"type": "string", "required": True},
            "filters": {"type": "object", "required": False}
        }
    },
    "query_metrics": {
        "description": "Query metrics from Signoz",
        "parameters": {
            "metric_name": {"type": "string", "required": True},
            "time_range": {"type": "string", "required": True},
            "filters": {"type": "object", "required": False}
        }
    },
    "query_traces": {
        "description": "Query traces from Signoz",
        "parameters": {
            "trace_id": {"type": "string", "required": False},
            "service": {"type": "string", "required": False},
            "time_range": {"type": "string", "required": False}
        }
    },
    "get_service_health": {
        "description": "Get service health status",
        "parameters": {
            "service": {"type": "string", "required": True}
        }
    }
}
```

#### 7.1.3 Implementation Example
```python
from mcp import MCPServer, Tool
from signoz_client import SignozClient

class SignozMCPServer(MCPServer):
    """MCP server for Signoz integration."""
    
    def __init__(self, signoz_url: str, api_token: str):
        super().__init__("signoz-mcp-server")
        self.client = SignozClient(signoz_url, api_token)
        self._register_tools()
    
    def _register_tools(self):
        """Register all available tools."""
        self.register_tool(Tool(
            name="query_logs",
            description="Query logs from Signoz",
            parameters={
                "service": {"type": "string", "required": True},
                "time_range": {"type": "string", "required": True},
                "filters": {"type": "object", "required": False}
            },
            handler=self._query_logs
        ))
        
        self.register_tool(Tool(
            name="query_metrics",
            description="Query metrics from Signoz",
            parameters={
                "metric_name": {"type": "string", "required": True},
                "time_range": {"type": "string", "required": True},
                "filters": {"type": "object", "required": False}
            },
            handler=self._query_metrics
        ))
    
    def _query_logs(self, service: str, time_range: str, filters: dict = None):
        """Query logs from Signoz."""
        return self.client.query_logs(service, time_range, filters)
    
    def _query_metrics(self, metric_name: str, time_range: str, filters: dict = None):
        """Query metrics from Signoz."""
        return self.client.query_metrics(metric_name, time_range, filters)
```

### 7.2 Kubernetes MCP Server

#### 7.2.1 Server Configuration
```yaml
# kubernetes-mcp-server.yaml
name: "kubernetes-mcp-server"
description: "MCP server for Kubernetes API"
version: "1.0.0"
endpoint: "https://kubernetes.default.svc.cluster.local"
authentication:
  type: "service-account"
  token_file: "/var/run/secrets/kubernetes.io/serviceaccount/token"
```

#### 7.2.2 Available Tools
```python
# Kubernetes MCP Server Tools
KUBERNETES_TOOLS = {
    "get_pod_status": {
        "description": "Get pod status and events",
        "parameters": {
            "namespace": {"type": "string", "required": True},
            "pod_name": {"type": "string", "required": True}
        }
    },
    "get_service_health": {
        "description": "Get service health status",
        "parameters": {
            "namespace": {"type": "string", "required": True},
            "service_name": {"type": "string", "required": True}
        }
    },
    "get_deployment_history": {
        "description": "Get deployment history",
        "parameters": {
            "namespace": {"type": "string", "required": True},
            "deployment_name": {"type": "string", "required": True}
        }
    },
    "get_events": {
        "description": "Get Kubernetes events",
        "parameters": {
            "namespace": {"type": "string", "required": False},
            "resource_type": {"type": "string", "required": False},
            "resource_name": {"type": "string", "required": False}
        }
    }
}
```

### 7.3 MongoDB MCP Server

#### 7.3.1 Server Configuration
```yaml
# mongodb-mcp-server.yaml
name: "mongodb-mcp-server"
description: "MCP server for MongoDB Atlas"
version: "1.0.0"
endpoint: "mongodb+srv://cluster.mongodb.net"
authentication:
  type: "mongodb"
  username: "${MONGODB_USERNAME}"
  password: "${MONGODB_PASSWORD}"
  database: "sre_agent"
```

#### 7.3.2 Available Tools
```python
# MongoDB MCP Server Tools
MONGODB_TOOLS = {
    "get_connection_metrics": {
        "description": "Get connection pool metrics",
        "parameters": {}
    },
    "get_query_performance": {
        "description": "Get query performance data",
        "parameters": {
            "time_range": {"type": "string", "required": True},
            "collection": {"type": "string", "required": False}
        }
    },
    "get_database_health": {
        "description": "Get database health status",
        "parameters": {}
    },
    "get_collection_stats": {
        "description": "Get collection statistics",
        "parameters": {
            "collection": {"type": "string", "required": True}
        }
    }
}
```

### 7.4 Slack MCP Server

#### 7.4.1 Server Configuration
```yaml
# slack-mcp-server.yaml
name: "slack-mcp-server"
description: "MCP server for Slack integration"
version: "1.0.0"
endpoint: "https://slack.com/api"
authentication:
  type: "bearer"
  token: "${SLACK_BOT_TOKEN}"
```

#### 7.4.2 Available Tools
```python
# Slack MCP Server Tools
SLACK_TOOLS = {
    "post_message": {
        "description": "Post message to Slack channel",
        "parameters": {
            "channel": {"type": "string", "required": True},
            "message": {"type": "string", "required": True},
            "thread_ts": {"type": "string", "required": False}
        }
    },
    "post_thread_reply": {
        "description": "Post reply to Slack thread",
        "parameters": {
            "channel": {"type": "string", "required": True},
            "thread_ts": {"type": "string", "required": True},
            "message": {"type": "string", "required": True}
        }
    },
    "get_channel_info": {
        "description": "Get channel information",
        "parameters": {
            "channel": {"type": "string", "required": True}
        }
    },
    "get_user_info": {
        "description": "Get user information",
        "parameters": {
            "user": {"type": "string", "required": True}
        }
    }
}
```

## 8. Development Tools References

### 8.1 Python Libraries
- **FastAPI**: [https://fastapi.tiangolo.com](https://fastapi.tiangolo.com)
- **Pydantic**: [https://pydantic-docs.helpmanual.io](https://pydantic-docs.helpmanual.io)
- **LangChain**: [https://python.langchain.com](https://python.langchain.com)
- **OpenAI Python SDK**: [https://github.com/openai/openai-python](https://github.com/openai/openai-python)
- **Kubernetes Python Client**: [https://github.com/kubernetes-client/python](https://github.com/kubernetes-client/python)

### 8.2 Testing Frameworks
- **pytest**: [https://pytest.org](https://pytest.org)
- **pytest-cov**: [https://pytest-cov.readthedocs.io](https://pytest-cov.readthedocs.io)
- **pytest-asyncio**: [https://pytest-asyncio.readthedocs.io](https://pytest-asyncio.readthedocs.io)
- **Selenium**: [https://selenium-python.readthedocs.io](https://selenium-python.readthedocs.io)

### 8.3 Monitoring and Observability
- **Prometheus**: [https://prometheus.io/docs](https://prometheus.io/docs)
- **Grafana**: [https://grafana.com/docs](https://grafana.com/docs)
- **Jaeger**: [https://www.jaegertracing.io/docs](https://www.jaegertracing.io/docs)
- **Fluentd**: [https://docs.fluentd.org](https://docs.fluentd.org)

## 9. Security References

### 9.1 Authentication and Authorization
- **JWT**: [https://jwt.io](https://jwt.io)
- **OAuth 2.0**: [https://oauth.net/2](https://oauth.net/2)
- **RBAC**: [https://kubernetes.io/docs/reference/access-authn-authz/rbac](https://kubernetes.io/docs/reference/access-authn-authz/rbac)
- **Azure AD**: [https://docs.microsoft.com/en-us/azure/active-directory](https://docs.microsoft.com/en-us/azure/active-directory)

### 9.2 Encryption and Security
- **TLS/SSL**: [https://tools.ietf.org/html/rfc8446](https://tools.ietf.org/html/rfc8446)
- **Cryptography**: [https://cryptography.io](https://cryptography.io)
- **Azure Key Vault**: [https://docs.microsoft.com/en-us/azure/key-vault](https://docs.microsoft.com/en-us/azure/key-vault)
- **Kubernetes Secrets**: [https://kubernetes.io/docs/concepts/configuration/secret](https://kubernetes.io/docs/concepts/configuration/secret)

## 10. Deployment and Operations References

### 10.1 Containerization
- **Docker**: [https://docs.docker.com](https://docs.docker.com)
- **Docker Compose**: [https://docs.docker.com/compose](https://docs.docker.com/compose)
- **Best Practices**: [https://docs.docker.com/develop/dev-best-practices](https://docs.docker.com/develop/dev-best-practices)

### 10.2 Kubernetes
- **Kubernetes Documentation**: [https://kubernetes.io/docs](https://kubernetes.io/docs)
- **Helm**: [https://helm.sh/docs](https://helm.sh/docs)
- **Istio**: [https://istio.io/latest/docs](https://istio.io/latest/docs)
- **ArgoCD**: [https://argo-cd.readthedocs.io](https://argo-cd.readthedocs.io)

### 10.3 CI/CD
- **GitHub Actions**: [https://docs.github.com/en/actions](https://docs.github.com/en/actions)
- **Azure DevOps**: [https://docs.microsoft.com/en-us/azure/devops](https://docs.microsoft.com/en-us/azure/devops)
- **Jenkins**: [https://www.jenkins.io/doc](https://www.jenkins.io/doc)

## 11. Industry Standards and Best Practices

### 11.1 SRE Practices
- **Google SRE Book**: [https://sre.google/sre-book/table-of-contents](https://sre.google/sre-book/table-of-contents)
- **SRE Workbook**: [https://sre.google/workbook/table-of-contents](https://sre.google/workbook/table-of-contents)
- **Incident Response**: [https://sre.google/sre-book/incident-response](https://sre.google/sre-book/incident-response)

### 11.2 AI/ML Best Practices
- **MLOps**: [https://ml-ops.org](https://ml-ops.org)
- **MLflow**: [https://mlflow.org/docs](https://mlflow.org/docs)
- **Kubeflow**: [https://www.kubeflow.org/docs](https://www.kubeflow.org/docs)

### 11.3 API Design
- **REST API Design**: [https://restfulapi.net](https://restfulapi.net)
- **OpenAPI Specification**: [https://swagger.io/specification](https://swagger.io/specification)
- **GraphQL**: [https://graphql.org/learn](https://graphql.org/learn)

## 12. Community and Support

### 12.1 Forums and Communities
- **Agno Community**: [https://agno.com/community](https://agno.com/community)
- **Unpage Slack**: [https://unpage.ai/slack](https://unpage.ai/slack)
- **MCP Community**: [https://github.com/modelcontextprotocol/community](https://github.com/modelcontextprotocol/community)
- **Signoz Community**: [https://github.com/SigNoz/signoz/discussions](https://github.com/SigNoz/signoz/discussions)

### 12.2 Support Channels
- **GitHub Issues**: For bug reports and feature requests
- **Slack Channels**: For real-time community support
- **Documentation**: For technical reference and guides
- **Stack Overflow**: For programming questions and answers

## 13. Version Compatibility Matrix

### 13.1 Framework Versions
| Component | Minimum Version | Recommended Version | Latest Version |
|-----------|----------------|-------------------|----------------|
| Python | 3.11 | 3.11 | 3.12 |
| Agno AgentOS | 1.0.0 | 1.2.0 | 1.5.0 |
| FastAPI | 0.100.0 | 0.104.0 | 0.108.0 |
| Kubernetes | 1.25 | 1.28 | 1.29 |
| MongoDB | 6.0 | 7.0 | 8.0 |
| Redis | 7.0 | 7.2 | 8.0 |

### 13.2 MCP Server Versions
| MCP Server | Version | Compatibility |
|------------|---------|---------------|
| Signoz MCP | 1.0.0 | Signoz 0.8+ |
| Kubernetes MCP | 1.0.0 | Kubernetes 1.25+ |
| MongoDB MCP | 1.0.0 | MongoDB 6.0+ |
| Slack MCP | 1.0.0 | Slack API v2.0+ |

## 14. Conclusion

This external references document provides comprehensive links to all relevant documentation, tools, and resources needed for the SRE Agent project. It serves as a central reference point for developers, operators, and stakeholders working with the system.

The MCP server definitions provide detailed specifications for integrating with various data sources, while the version compatibility matrix ensures that all components work together seamlessly. Regular updates to this document will ensure that it remains current and useful throughout the project lifecycle.
