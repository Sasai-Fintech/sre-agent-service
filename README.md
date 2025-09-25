# SRE Agent Service

An intelligent AI-powered SRE agent that automatically analyzes infrastructure alerts and provides comprehensive Root Cause Analysis (RCA) directly within Slack threads.

## 🚀 Overview

The SRE Agent is designed to eliminate manual log searching and accelerate incident response by leveraging AI-powered analysis across multiple observability data sources including Signoz, Kubernetes, MongoDB Atlas, and more.

## ✨ Key Features

- **Automated Alert Analysis**: Process alerts from Slack across all infrastructure categories
- **Intelligent RCA**: Deliver detailed root cause analysis within 5 minutes
- **Multi-Source Integration**: Connect with Signoz, Kubernetes, MongoDB Atlas, and Slack
- **Specialized Agents**: Network, Database, Infrastructure, Application, and Resource agents
- **Real-time Processing**: Handle up to 100 concurrent analysis requests
- **Human-in-the-Loop**: Thread-based responses with human validation

## 🏗️ Architecture

The system is built using:
- **Agent Framework**: Agno AgentOS (400x faster than LangChain)
- **AI Engine**: Azure OpenAI with GPT-4 and GPT-3.5-turbo
- **Integration**: Model Context Protocol (MCP) for standardized tool access
- **Infrastructure**: Azure Kubernetes Service (AKS)
- **Data Sources**: Signoz, ClickHouse, MongoDB Atlas, Kubernetes API

## 📁 Project Structure

```
sre-agent-service/
├── docs/                          # Project documentation
│   ├── 01-functional-requirements.md
│   ├── 02-technology-choices.md
│   ├── 03-architecture-diagrams.md
│   └── 04-milestones-task-breakdown.md
├── src/                           # Source code
│   ├── agents/                    # Specialized agents
│   ├── mcp_servers/              # MCP server implementations
│   ├── ai_engine/                # AI analysis engine
│   └── integrations/             # External service integrations
├── tests/                        # Test suites
├── config/                       # Configuration files
├── scripts/                      # Deployment and utility scripts
└── README.md
```

## 🚀 Quick Start

### Prerequisites

- Python 3.11+
- Azure OpenAI API access
- Kubernetes cluster (AKS recommended)
- Access to Signoz, MongoDB Atlas, and Slack APIs

### Installation

1. Clone the repository:
```bash
git clone https://github.com/your-username/sre-agent-service.git
cd sre-agent-service
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Configure environment variables:
```bash
cp .env.example .env
# Edit .env with your configuration
```

4. Deploy to Kubernetes:
```bash
kubectl apply -f config/kubernetes/
```

## 📊 Performance Metrics

- **Response Time**: < 5 minutes for 95% of requests
- **Accuracy**: > 85% accurate root cause identification
- **Throughput**: 100 concurrent requests
- **Uptime**: 99.9% availability

## 🔧 Configuration

The system uses YAML-based configuration for agent routing and behavior:

```yaml
# Example agent configuration
description: "Investigate SSL/TLS connection failures"
prompt: |
  - Extract domain/hostname from alert
  - Check certificate expiration via MCP tools
  - Analyze network connectivity patterns
  - Correlate with recent deployments
  - Provide remediation steps
tools:
  - "signoz_logs_query"
  - "kubernetes_pod_status"
  - "mongodb_connection_metrics"
  - "slack_thread_reply"
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 Support

- **Documentation**: Check the `docs/` directory for detailed documentation
- **Issues**: Report bugs and feature requests via [GitHub Issues](https://github.com/your-username/sre-agent-service/issues)
- **Discussions**: Join our [GitHub Discussions](https://github.com/your-username/sre-agent-service/discussions) for questions and ideas

## 🗺️ Roadmap

- [ ] Phase 1: Foundation & Infrastructure Setup (Weeks 1-2)
- [ ] Phase 2: Data Integration & MCP Servers (Weeks 3-4)
- [ ] Phase 3: Specialized Agents & Routing (Weeks 5-6)
- [ ] Phase 4: AI Analysis Engine (Weeks 7-8)
- [ ] Phase 5: Slack Integration & User Experience (Weeks 9-10)
- [ ] Phase 6: Production Deployment & Optimization (Weeks 11-12)

## 🙏 Acknowledgments

- Inspired by [Cleric.ai](https://cleric.ai) and [FuzzyLabs SRE-Agent](https://github.com/fuzzylabs/sre-agent)
- Built with [Agno AgentOS](https://github.com/agno-ai/agno-agentos)
- Powered by [Azure OpenAI](https://azure.microsoft.com/en-us/products/ai-services/openai-service)

---

**Status**: 🚧 In Development | **Version**: 0.1.0 | **Last Updated**: January 2025
