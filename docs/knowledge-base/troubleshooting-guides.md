# Troubleshooting Guides

## Common Issues & Solutions

### Agent Instantiation Failures

#### Symptoms
- Agents fail to start
- High memory usage
- Slow response times
- Connection timeouts

#### Root Causes
1. **Memory exhaustion**: Too many concurrent agents
2. **Connection pool exhaustion**: External API rate limits
3. **Configuration errors**: Invalid environment variables
4. **Resource constraints**: Insufficient CPU/memory

#### Solutions
```bash
# Check agent status
kubectl get pods -n sre-agent

# Check resource usage
kubectl top pods -n sre-agent

# Check logs
kubectl logs -f deployment/sre-agent -n sre-agent

# Restart deployment
kubectl rollout restart deployment/sre-agent -n sre-agent
```

#### Prevention
- Implement proper resource limits
- Use connection pooling
- Monitor resource usage
- Implement circuit breakers

### MCP Server Connection Issues

#### Symptoms
- External API calls failing
- Data retrieval timeouts
- Incomplete analysis results
- Error messages in logs

#### Root Causes
1. **Network connectivity**: Firewall or DNS issues
2. **Authentication failures**: Invalid API keys
3. **Rate limiting**: Exceeding API quotas
4. **Service unavailability**: External service down

#### Solutions
```python
# Check MCP server health
curl -X GET "http://mcp-server:8080/health"

# Verify API credentials
curl -X GET "https://api.signoz.com/v1/health" \
  -H "Authorization: Bearer $SIGNOZ_API_KEY"

# Test connectivity
kubectl exec -it pod/sre-agent -- ping api.signoz.com
```

#### Prevention
- Implement health checks
- Use retry logic with exponential backoff
- Monitor API quotas
- Implement circuit breakers

### AI Analysis Failures

#### Symptoms
- Analysis requests timing out
- Incomplete RCA reports
- High token usage
- Poor analysis quality

#### Root Causes
1. **Context window overflow**: Too much data for AI model
2. **Prompt engineering issues**: Poor prompt design
3. **Model limitations**: Insufficient model capabilities
4. **Data quality issues**: Poor input data

#### Solutions
```python
# Check AI engine health
curl -X GET "http://ai-engine:8080/health"

# Verify OpenAI connection
curl -X POST "https://api.openai.com/v1/models" \
  -H "Authorization: Bearer $OPENAI_API_KEY"

# Check token usage
kubectl logs deployment/ai-engine | grep "token_usage"
```

#### Prevention
- Implement context size limits
- Optimize prompts for specific use cases
- Use appropriate model for task complexity
- Validate input data quality

### Slack Integration Issues

#### Symptoms
- Messages not posting to Slack
- Thread management failures
- Permission errors
- Webhook failures

#### Root Causes
1. **Invalid tokens**: Expired or incorrect Slack tokens
2. **Permission issues**: Insufficient bot permissions
3. **Rate limiting**: Exceeding Slack API limits
4. **Webhook configuration**: Incorrect webhook setup

#### Solutions
```bash
# Check Slack bot status
curl -X GET "https://slack.com/api/auth.test" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN"

# Verify webhook configuration
curl -X POST "$SLACK_WEBHOOK_URL" \
  -H "Content-Type: application/json" \
  -d '{"text": "Test message"}'

# Check bot permissions
curl -X GET "https://slack.com/api/bots.info" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN"
```

#### Prevention
- Regularly rotate API tokens
- Implement proper error handling
- Monitor API usage
- Test webhook configuration

## Performance Troubleshooting

### Slow Response Times

#### Symptoms
- Analysis taking > 5 minutes
- High CPU usage
- Memory leaks
- Database connection issues

#### Diagnosis
```bash
# Check response times
kubectl logs deployment/sre-agent | grep "response_time"

# Monitor resource usage
kubectl top pods -n sre-agent

# Check database connections
kubectl exec -it pod/sre-agent -- netstat -an | grep :27017
```

#### Solutions
- Optimize database queries
- Implement caching
- Scale resources
- Optimize AI prompts

### Memory Issues

#### Symptoms
- High memory usage
- Out of memory errors
- Slow garbage collection
- Memory leaks

#### Diagnosis
```bash
# Check memory usage
kubectl top pods -n sre-agent

# Check memory limits
kubectl describe pod sre-agent

# Monitor garbage collection
kubectl logs deployment/sre-agent | grep "GC"
```

#### Solutions
- Increase memory limits
- Optimize data structures
- Implement connection pooling
- Monitor memory usage patterns

## Security Troubleshooting

### Authentication Failures

#### Symptoms
- 401 Unauthorized errors
- Token validation failures
- Permission denied errors
- Session timeouts

#### Diagnosis
```bash
# Check authentication logs
kubectl logs deployment/sre-agent | grep "auth"

# Verify token validity
kubectl exec -it pod/sre-agent -- curl -H "Authorization: Bearer $TOKEN" \
  "https://api.service.com/v1/verify"
```

#### Solutions
- Refresh expired tokens
- Check token permissions
- Verify authentication configuration
- Implement token rotation

### Data Security Issues

#### Symptoms
- Sensitive data in logs
- Unencrypted data transmission
- Access control failures
- Audit trail gaps

#### Diagnosis
```bash
# Check for sensitive data in logs
kubectl logs deployment/sre-agent | grep -i "password\|token\|key"

# Verify encryption
kubectl exec -it pod/sre-agent -- curl -I https://api.service.com
```

#### Solutions
- Implement data masking
- Enable encryption in transit
- Review access controls
- Implement audit logging

## Monitoring & Alerting

### Health Check Failures

#### Symptoms
- Health check endpoints returning errors
- Kubernetes readiness/liveness probe failures
- Service unavailability
- Load balancer issues

#### Diagnosis
```bash
# Check health endpoints
curl -X GET "http://sre-agent:8080/health"
curl -X GET "http://sre-agent:8080/ready"

# Check Kubernetes probes
kubectl describe pod sre-agent

# Check service status
kubectl get svc -n sre-agent
```

#### Solutions
- Fix health check logic
- Adjust probe timeouts
- Check service configuration
- Verify load balancer setup

### Alerting Issues

#### Symptoms
- Alerts not firing
- False positive alerts
- Missing alerts
- Alert fatigue

#### Diagnosis
```bash
# Check alerting configuration
kubectl get configmap alerting-config -n sre-agent

# Check alerting logs
kubectl logs deployment/alerting-service

# Test alerting
curl -X POST "http://alerting-service:8080/test-alert"
```

#### Solutions
- Review alert thresholds
- Optimize alert rules
- Implement alert correlation
- Reduce noise in alerts

## Recovery Procedures

### Service Recovery

#### Complete Service Failure
1. Check Kubernetes cluster status
2. Verify resource availability
3. Restart failed deployments
4. Check external dependencies
5. Validate service functionality

#### Partial Service Failure
1. Identify affected components
2. Check component health
3. Restart affected pods
4. Verify data consistency
5. Monitor recovery progress

#### Data Recovery
1. Check data backup status
2. Verify data integrity
3. Restore from backup if needed
4. Validate data consistency
5. Update data replication

### Rollback Procedures

#### Application Rollback
```bash
# Rollback deployment
kubectl rollout undo deployment/sre-agent -n sre-agent

# Check rollback status
kubectl rollout status deployment/sre-agent -n sre-agent

# Verify rollback
kubectl get pods -n sre-agent
```

#### Configuration Rollback
```bash
# Restore configuration
kubectl apply -f config/previous-version/

# Verify configuration
kubectl get configmap -n sre-agent

# Test functionality
curl -X GET "http://sre-agent:8080/health"
```

## Prevention Strategies

### Proactive Monitoring
- Implement comprehensive health checks
- Monitor key performance indicators
- Set up automated alerting
- Regular security scans

### Regular Maintenance
- Update dependencies regularly
- Rotate credentials periodically
- Review and update configurations
- Perform regular backups

### Testing Strategies
- Implement comprehensive test suites
- Perform regular load testing
- Conduct security testing
- Validate disaster recovery procedures
