# Prometheus Alerting Framework

Production-ready alerting framework for Prometheus + Alertmanager + Grafana. Standardized alerting rules, routing configurations, and Grafana dashboards as code for multi-cluster Kubernetes environments.

## Overview

Managing alerting across multiple Kubernetes clusters and AWS accounts gets messy fast. This framework provides a structured, version-controlled approach to:

- **Alerting Rules** — SLO-based alerts for latency, error rates, and saturation
- **Recording Rules** — Pre-aggregated PromQL for dashboard performance
- **Alertmanager Config** — Routing, inhibition, and silencing with PagerDuty/Slack
- **Grafana Dashboards** — JSON dashboards provisioned as code

## Structure

```
├── alertmanager/
│   ├── alertmanager.yml          # Main routing config
│   └── templates/                # Notification templates
├── prometheus/
│   ├── rules/                    # Alerting rules by service
│   │   ├── slo-latency.yml
│   │   ├── slo-availability.yml
│   │   ├── infrastructure.yml
│   │   └── kubernetes.yml
│   └── recording/                # Recording rules
│       ├── slo-recording.yml
│       └── aggregations.yml
├── grafana/
│   └── dashboards/               # Dashboard JSON
│       ├── slo-overview.json
│       ├── cluster-health.json
│       └── service-latency.json
├── scripts/
│   ├── validate_rules.sh         # CI validation
│   └── deploy_dashboards.py      # Grafana API deployment
└── docs/
    └── runbook.md
```

## Alerting Philosophy

- **SLO-driven**: Alerts fire based on error budget burn rate, not arbitrary thresholds
- **Low noise**: Multi-window burn rate alerts reduce false positives
- **Actionable**: Every alert links to a runbook with remediation steps
- **Tiered severity**: critical → PagerDuty, warning → Slack, info → dashboard only

## Quick Start

```bash
# Validate all rules
./scripts/validate_rules.sh

# Deploy dashboards to Grafana
python scripts/deploy_dashboards.py --grafana-url http://grafana:3000 --api-key $GRAFANA_API_KEY

# Test alertmanager routing
amtool check-config alertmanager/alertmanager.yml
```

## SLO Alert Examples

### Latency SLO (99th percentile < 500ms)
```yaml
- alert: HighLatencyBurnRate
  expr: |
    (
      sum(rate(http_request_duration_seconds_bucket{le="0.5"}[5m])) by (service)
      /
      sum(rate(http_request_duration_seconds_count[5m])) by (service)
    ) < 0.99
  for: 5m
  labels:
    severity: warning
    slo: latency
```

### Availability SLO (99.9% success rate)
```yaml
- alert: HighErrorBurnRate
  expr: |
    1 - (
      sum(rate(http_requests_total{code!~"5.."}[5m])) by (service)
      /
      sum(rate(http_requests_total[5m])) by (service)
    ) > 0.001
  for: 5m
  labels:
    severity: critical
    slo: availability
```

## Requirements

- Prometheus 2.40+
- Alertmanager 0.25+
- Grafana 9.0+
- Python 3.9+ (for dashboard deployment)

## License

MIT

<!-- updated: 2025-07-30 -->

<!-- updated: 2025-08-22 -->

<!-- updated: 2025-09-15 -->

<!-- updated: 2025-10-28 -->

<!-- updated: 2025-11-12 -->

<!-- updated: 2025-12-05 -->

<!-- updated: 2026-01-18 -->

<!-- 2024-07-08T14:45:00 -->

<!-- 2024-08-19T10:00:00 -->

<!-- 2024-09-30T15:15:00 -->

<!-- 2024-11-11T11:30:00 -->

<!-- 2024-12-23T09:45:00 -->

<!-- 2025-02-10T14:00:00 -->

<!-- 2025-03-24T10:15:00 -->

<!-- 2025-05-12T15:30:00 -->

<!-- 2025-06-30T11:45:00 -->

<!-- 2025-08-18T09:00:00 -->

<!-- 2025-10-06T14:15:00 -->

<!-- 2025-11-17T10:30:00 -->

<!-- 2025-12-29T15:45:00 -->

<!-- 2026-02-09T11:00:00 -->

<!-- 2024-07-08T14:45:00 -->

<!-- 2024-08-19T10:00:00 -->

<!-- 2024-09-30T15:15:00 -->

<!-- 2024-11-11T11:30:00 -->

<!-- 2024-12-23T09:45:00 -->

<!-- 2025-02-10T14:00:00 -->

<!-- 2025-03-24T10:15:00 -->

<!-- 2025-05-12T15:30:00 -->

<!-- 2025-06-30T11:45:00 -->

<!-- 2025-08-18T09:00:00 -->

<!-- 2025-10-06T14:15:00 -->

<!-- 2025-11-17T10:30:00 -->

<!-- 2025-12-29T15:45:00 -->

<!-- 2026-02-09T11:00:00 -->

<!-- 2024-06-18T14:45:00 -->

<!-- 2024-06-19T10:00:00 -->

<!-- 2024-08-19T15:15:00 -->

<!-- 2024-08-20T11:30:00 -->

<!-- 2024-11-11T09:45:00 -->

<!-- 2025-01-10T14:00:00 -->

<!-- 2025-01-11T10:15:00 -->

<!-- 2025-04-12T15:30:00 -->

<!-- 2025-07-30T11:45:00 -->

<!-- 2025-07-31T09:00:00 -->

<!-- 2025-10-06T14:15:00 -->

<!-- 2025-12-29T10:30:00 -->

<!-- 2026-02-09T15:45:00 -->

<!-- 2026-03-17T11:00:00 -->

<!-- 2024-06-05T14:45:00 -->

<!-- 2024-06-06T10:00:00 -->

<!-- 2024-08-13T15:15:00 -->

<!-- 2024-08-14T11:30:00 -->

<!-- 2024-11-19T09:45:00 -->

<!-- 2025-01-28T14:00:00 -->

<!-- 2025-01-29T10:15:00 -->

<!-- 2025-04-22T15:30:00 -->

<!-- 2025-07-29T11:45:00 -->

<!-- 2025-07-30T09:00:00 -->

<!-- 2025-10-28T14:15:00 -->

<!-- 2026-01-06T10:30:00 -->

<!-- 2026-02-24T15:45:00 -->

<!-- 2026-04-07T11:00:00 -->

<!-- 2024-07-27T14:54:00 -->

<!-- 2024-08-16T12:37:00 -->

<!-- 2024-08-23T13:06:00 -->

<!-- 2024-10-09T09:53:00 -->

<!-- 2024-11-04T13:03:00 -->

<!-- 2024-11-09T17:01:00 -->

<!-- 2025-01-17T08:03:00 -->

<!-- 2025-02-07T17:35:00 -->

<!-- 2025-03-30T12:23:00 -->

<!-- 2025-04-06T15:04:00 -->

<!-- 2025-06-17T12:01:00 -->

<!-- 2025-08-09T14:31:00 -->

<!-- 2025-10-10T13:04:00 -->

<!-- 2025-12-04T10:15:00 -->

<!-- 2025-12-26T09:11:00 -->

<!-- 2025-12-30T15:29:00 -->

<!-- 2026-01-08T13:09:00 -->

<!-- 2026-04-05T13:04:00 -->

<!-- 2026-04-23T17:55:00 -->

<!-- 2024-09-17T08:10:00 -->

<!-- 2024-11-27T11:50:00 -->

<!-- 2025-01-15T14:20:00 -->

<!-- 2025-02-18T15:12:00 -->

<!-- 2025-07-14T12:53:00 -->

<!-- 2025-09-07T08:42:00 -->

<!-- 2025-10-28T11:20:00 -->

<!-- 2026-02-03T11:42:00 -->

<!-- 2026-03-08T14:53:00 -->

<!-- 2024-09-17T08:10:00 -->

<!-- 2024-11-27T11:50:00 -->

<!-- 2025-01-15T14:20:00 -->

<!-- 2025-02-18T15:12:00 -->

<!-- 2025-07-14T12:53:00 -->

<!-- 2025-09-07T08:42:00 -->

<!-- 2025-10-28T11:20:00 -->

<!-- 2026-02-03T11:42:00 -->

<!-- 2026-03-08T14:53:00 -->

<!-- 2024-09-17T08:10:00 -->

<!-- 2024-11-27T11:50:00 -->

<!-- 2025-01-15T14:20:00 -->

<!-- 2025-02-18T15:12:00 -->

<!-- 2025-07-14T12:53:00 -->

<!-- 2025-09-07T08:42:00 -->
