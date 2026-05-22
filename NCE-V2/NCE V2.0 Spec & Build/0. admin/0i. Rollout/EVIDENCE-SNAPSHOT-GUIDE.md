# Evidence Snapshot Guide

---
Status: COMPLETE
Version: v1.0.0
Template-Version: 1.0
Section: 0i Rollout
Purpose: How to capture evidence before rollback for root cause analysis
---

## Overview

**CRITICAL:** Before executing a rollback, capture evidence of the failing state. Once rollback completes, the failing environment is wiped and root cause analysis becomes guesswork.

**Time Limit:** Evidence capture should take < 5 minutes. Do not significantly delay rollback to gather evidence.

---

## Evidence Capture Checklist

| Evidence Type | Priority | Captured | Location |
|---------------|----------|----------|----------|
| Application logs (last 30 min) | P1 | ☐ | |
| Error samples | P1 | ☐ | |
| Metrics snapshot | P1 | ☐ | |
| Heap dump (JVM) | P2 | ☐ / N/A | |
| Thread dump | P2 | ☐ / N/A | |
| Network traces | P3 | ☐ | |
| Database query logs | P3 | ☐ | |

**P1 = Must capture, P2 = Capture if time permits, P3 = Nice to have**

---

## Evidence Capture Commands

### Application Logs

```bash
# Export last 30 minutes of logs
# Kubernetes
kubectl logs {{POD_NAME}} --since=30m > /tmp/app-logs-$(date +%Y%m%d-%H%M%S).log

# Docker
docker logs --since=30m {{CONTAINER_ID}} > /tmp/app-logs-$(date +%Y%m%d-%H%M%S).log

# CloudWatch
aws logs get-log-events \
  --log-group-name {{LOG_GROUP}} \
  --log-stream-name {{LOG_STREAM}} \
  --start-time $(date -d '30 minutes ago' +%s)000 \
  > /tmp/app-logs-$(date +%Y%m%d-%H%M%S).json

# Upload to S3
aws s3 cp /tmp/app-logs-*.* s3://{{BUCKET}}/incidents/$(date +%Y-%m-%d)/
```

### Error Samples

```bash
# Export from error tracking (Sentry example)
# Usually done via dashboard export or API

# Datadog
curl -X GET "https://api.datadoghq.com/api/v2/logs/events" \
  -H "DD-API-KEY: {{API_KEY}}" \
  -H "DD-APPLICATION-KEY: {{APP_KEY}}" \
  -d '{"filter": {"query": "status:error", "from": "now-30m", "to": "now"}}' \
  > /tmp/errors-$(date +%Y%m%d-%H%M%S).json
```

### Metrics Snapshot

```bash
# Prometheus (example query)
curl -G 'http://prometheus:9090/api/v1/query_range' \
  --data-urlencode 'query=http_requests_total{status=~"5.."}' \
  --data-urlencode 'start='$(date -d '30 minutes ago' +%s) \
  --data-urlencode 'end='$(date +%s) \
  --data-urlencode 'step=15s' \
  > /tmp/metrics-$(date +%Y%m%d-%H%M%S).json

# Or export dashboard snapshot from Grafana/Datadog
```

### Heap Dump (JVM Applications)

```bash
# Get process ID
PID=$(pgrep -f {{APP_NAME}})

# Capture heap dump
jmap -dump:format=b,file=/tmp/heap-$(date +%Y%m%d-%H%M%S).hprof $PID

# WARNING: This can pause the JVM for seconds to minutes
# Only capture if application is already failing
```

### Thread Dump (JVM Applications)

```bash
# Get process ID
PID=$(pgrep -f {{APP_NAME}})

# Capture thread dump
jstack $PID > /tmp/threads-$(date +%Y%m%d-%H%M%S).txt

# This is fast and low-impact
```

### Network Traces

```bash
# Capture network traffic (requires root)
timeout 60 tcpdump -i any -w /tmp/network-$(date +%Y%m%d-%H%M%S).pcap \
  'port {{APP_PORT}}'

# Or export from APM tool (Datadog, New Relic, etc.)
```

---

## Evidence Storage

### Storage Location

```
s3://{{BUCKET}}/incidents/{{YYYY-MM-DD}}/{{INCIDENT_ID}}/
├── app-logs-{{TIMESTAMP}}.log
├── errors-{{TIMESTAMP}}.json
├── metrics-{{TIMESTAMP}}.json
├── heap-{{TIMESTAMP}}.hprof (if applicable)
├── threads-{{TIMESTAMP}}.txt (if applicable)
├── network-{{TIMESTAMP}}.pcap (if applicable)
└── README.md (incident context)
```

### Upload Commands

```bash
# Set incident folder
INCIDENT_DATE=$(date +%Y-%m-%d)
INCIDENT_ID="INC-${INCIDENT_DATE}-001"
S3_PATH="s3://{{BUCKET}}/incidents/${INCIDENT_DATE}/${INCIDENT_ID}"

# Upload all evidence
aws s3 cp /tmp/app-logs-*.* ${S3_PATH}/
aws s3 cp /tmp/errors-*.* ${S3_PATH}/
aws s3 cp /tmp/metrics-*.* ${S3_PATH}/
aws s3 cp /tmp/heap-*.* ${S3_PATH}/ 2>/dev/null || true
aws s3 cp /tmp/threads-*.* ${S3_PATH}/ 2>/dev/null || true

# Create README
echo "Incident: ${INCIDENT_ID}" > /tmp/README.md
echo "Captured: $(date)" >> /tmp/README.md
echo "RC Tag: {{RC_TAG}}" >> /tmp/README.md
echo "Reason: {{BRIEF_REASON}}" >> /tmp/README.md
aws s3 cp /tmp/README.md ${S3_PATH}/
```

---

## Quick Reference Script

Save this as `capture-evidence.sh`:

```bash
#!/bin/bash
# Usage: ./capture-evidence.sh <incident-id> <reason>

INCIDENT_ID=${1:-"INC-$(date +%Y%m%d-%H%M%S)"}
REASON=${2:-"Unspecified"}
TIMESTAMP=$(date +%Y%m%d-%H%M%S)
EVIDENCE_DIR="/tmp/evidence-${INCIDENT_ID}"
S3_BUCKET="{{YOUR_BUCKET}}"

echo "🔴 Capturing evidence for incident: ${INCIDENT_ID}"
echo "⏱️  Time limit: 5 minutes"
echo ""

mkdir -p ${EVIDENCE_DIR}

# Capture logs (customize for your environment)
echo "📋 Capturing application logs..."
kubectl logs {{POD_SELECTOR}} --since=30m > ${EVIDENCE_DIR}/app-logs.log 2>/dev/null || \
  echo "Could not capture Kubernetes logs" > ${EVIDENCE_DIR}/app-logs.log

# Capture metrics
echo "📊 Capturing metrics snapshot..."
# Add your metrics capture command here

# Capture errors
echo "❌ Capturing error samples..."
# Add your error capture command here

# Create README
echo "📝 Creating README..."
cat > ${EVIDENCE_DIR}/README.md << EOF
# Incident Evidence

- **Incident ID:** ${INCIDENT_ID}
- **Captured:** $(date)
- **Reason:** ${REASON}
- **RC Tag:** {{RC_TAG}}

## Contents

- app-logs.log - Application logs (last 30 min)
- metrics.json - Metrics snapshot
- errors.json - Error samples

## Notes

{{Add any relevant context}}
EOF

# Upload to S3
echo "☁️  Uploading to S3..."
aws s3 sync ${EVIDENCE_DIR} s3://${S3_BUCKET}/incidents/$(date +%Y-%m-%d)/${INCIDENT_ID}/

echo ""
echo "✅ Evidence captured and uploaded"
echo "📁 Location: s3://${S3_BUCKET}/incidents/$(date +%Y-%m-%d)/${INCIDENT_ID}/"
echo ""
echo "🔄 Now proceed with rollback"
```

---

## Platform-Specific Guides

### Kubernetes

```bash
# Get failing pod logs
kubectl logs -l app={{APP_NAME}} --since=30m --all-containers > /tmp/k8s-logs.log

# Describe pods for events
kubectl describe pods -l app={{APP_NAME}} > /tmp/k8s-describe.txt

# Get recent events
kubectl get events --sort-by='.lastTimestamp' > /tmp/k8s-events.txt
```

### AWS ECS

```bash
# Get task logs
aws logs get-log-events \
  --log-group-name /ecs/{{TASK_DEFINITION}} \
  --log-stream-name {{LOG_STREAM}} \
  --start-time $(date -d '30 minutes ago' +%s)000

# Describe tasks
aws ecs describe-tasks --cluster {{CLUSTER}} --tasks {{TASK_ARN}}
```

### Serverless (Lambda)

```bash
# Get function logs
aws logs filter-log-events \
  --log-group-name /aws/lambda/{{FUNCTION_NAME}} \
  --start-time $(date -d '30 minutes ago' +%s)000 \
  --filter-pattern "ERROR"
```

---

## What to Capture vs Skip

### Always Capture (< 2 min)

- Application logs
- Error samples
- Basic metrics (error rate, latency)

### Capture If Time Permits (2-5 min)

- Thread dumps
- Detailed metrics
- Recent deployment events

### Skip During Emergency

- Full heap dumps (too slow)
- Network packet captures (unless network issue)
- Historical data (can get later)

---

## Post-Capture Checklist

After capturing evidence and before rollback:

- [ ] Evidence uploaded to S3/storage
- [ ] Location noted in INCIDENT-REPORT.md
- [ ] README created with context
- [ ] Team notified of evidence location
- [ ] Proceed with rollback

---

## Evidence Retention

| Type | Retention | Notes |
|------|-----------|-------|
| Logs | 30 days | Auto-delete after |
| Metrics | 90 days | For trend analysis |
| Heap dumps | 7 days | Large files, delete after analysis |
| Incident reports | Permanent | In repository |

---
Generated: January 2025
Section: 0i Rollout
Template: EVIDENCE-SNAPSHOT-GUIDE.md v1.0
---
