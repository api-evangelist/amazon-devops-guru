# Amazon DevOps Guru (amazon-devops-guru)
Amazon DevOps Guru is a machine learning-powered service that makes it easy to improve an application's operational performance and availability. It detects behaviors that deviate from normal operating patterns so you can identify operational issues long before they impact your customers.

**URL:** [Visit Amazon DevOps Guru](https://aws.amazon.com/devops-guru/)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags:

 - Anomaly Detection, AWS, DevOps, Machine Learning, Operational Intelligence

## Timestamps

- **Created:** 2026-03-16
- **Modified:** 2026-04-19

## APIs

### Amazon DevOps Guru API
The Amazon DevOps Guru API provides programmatic access to manage resource collections, insights, anomalies, and recommendations for improving application operational performance and availability. Covers 31 operations including insight management, anomaly investigation, remediation recommendations, notification configuration, and AWS Organizations integration.

**Human URL:** [https://aws.amazon.com/devops-guru/](https://aws.amazon.com/devops-guru/)

#### Tags:

 - Anomaly Detection, DevOps, Machine Learning, Operational Intelligence

#### Properties

- [Documentation](https://docs.aws.amazon.com/devops-guru/latest/APIReference/Welcome.html)
- [OpenAPI](openapi/amazon-devops-guru-openapi.yaml)
- [GettingStarted](https://aws.amazon.com/devops-guru/getting-started/)
- [Pricing](https://aws.amazon.com/devops-guru/pricing/)
- [FAQ](https://aws.amazon.com/devops-guru/faqs/)
- [JSONSchema - Proactive Insight](json-schema/amazon-devops-guru-proactive-insight-schema.json)
- [JSONSchema - Reactive Insight](json-schema/amazon-devops-guru-reactive-insight-schema.json)
- [JSONSchema - Recommendation](json-schema/amazon-devops-guru-recommendation-schema.json)
- [JSONSchema - Proactive Anomaly](json-schema/amazon-devops-guru-proactive-anomaly-schema.json)
- [JSONSchema - Reactive Anomaly](json-schema/amazon-devops-guru-reactive-anomaly-schema.json)
- [JSONStructure - Proactive Insight](json-structure/amazon-devops-guru-proactive-insight-structure.json)
- [JSONStructure - Reactive Insight](json-structure/amazon-devops-guru-reactive-insight-structure.json)
- [JSONStructure - Recommendation](json-structure/amazon-devops-guru-recommendation-structure.json)
- [JSON-LD](json-ld/amazon-devops-guru-context.jsonld)
- [Example - Proactive Insight](examples/amazon-devops-guru-proactive-insight-example.json)
- [Example - Reactive Insight](examples/amazon-devops-guru-reactive-insight-example.json)
- [Example - Recommendation](examples/amazon-devops-guru-recommendation-example.json)

## Common Properties

- [Portal](https://aws.amazon.com/devops-guru/)
- [Website](https://aws.amazon.com/devops-guru/)
- [Documentation](https://docs.aws.amazon.com/devops-guru/)
- [TermsOfService](https://aws.amazon.com/service-terms/)
- [PrivacyPolicy](https://aws.amazon.com/privacy/)
- [Support](https://aws.amazon.com/premiumsupport/)
- [Blog](https://aws.amazon.com/blogs/devops/category/artificial-intelligence/amazon-devops-guru/)
- [GitHubOrganization](https://github.com/aws)
- [Console](https://console.aws.amazon.com/devops-guru/)
- [SignUp](https://portal.aws.amazon.com/billing/signup)
- [Login](https://signin.aws.amazon.com/)
- [StatusPage](https://health.aws.amazon.com/health/status)
- [Contact](https://aws.amazon.com/contact-us/)
- [SpectralRules](rules/amazon-devops-guru-spectral-rules.yml)
- [Vocabulary](vocabulary/amazon-devops-guru-vocabulary.yaml)
- [NaftikoCapability](capabilities/operational-intelligence.yaml)

## Features

| Name | Description |
|------|-------------|
| ML-Powered Anomaly Detection | Uses machine learning to detect behavioral deviations across hundreds of AWS metrics without manual threshold configuration. |
| Proactive Insights | Identifies anomalies before they become operational issues, allowing teams to remediate before customer impact. |
| Reactive Insights | Surfaces insights when active operational issues are detected to accelerate root cause analysis. |
| Actionable Recommendations | Provides specific remediation recommendations with links to relevant documentation and AWS console pages. |
| CloudWatch Logs Integration | Analyzes CloudWatch Logs for log-based anomalies to include log patterns in operational insights. |
| AWS OpsCenter Integration | Automatically creates OpsCenter OpsItems for detected insights to streamline incident management. |
| CloudFormation-Based Coverage | Define which applications to monitor by specifying CloudFormation stack names for precise application-scoped coverage. |
| Organizations Integration | Enable DevOps Guru across an entire AWS Organization to centrally monitor all accounts and regions. |

## Use Cases

| Name | Description |
|------|-------------|
| Proactive Operational Monitoring | Detect potential issues in application behavior before they impact end users using ML-powered proactive insights. |
| Incident Root Cause Analysis | Rapidly identify the root cause of operational incidents by correlating anomalies, events, and recommendations. |
| Application Performance Optimization | Use continuous behavioral monitoring to identify performance bottlenecks and optimization opportunities. |
| Multi-Account Operations | Monitor operational health across all accounts in an AWS Organization from a single pane of glass. |
| DevOps Pipeline Integration | Integrate DevOps Guru insights into CI/CD pipelines to gate deployments on operational health status. |

## Integrations

| Name | Description |
|------|-------------|
| Amazon CloudWatch | Ingests CloudWatch metrics and logs for anomaly detection and event correlation. |
| AWS CloudFormation | Uses CloudFormation stacks to define application boundaries for targeted monitoring coverage. |
| AWS OpsCenter | Automatically creates OpsCenter OpsItems for detected insights to enable incident management workflows. |
| Amazon SNS | Sends insight notifications to SNS topics for routing to teams via email, Slack, PagerDuty, or other channels. |
| AWS Organizations | Enables organization-wide monitoring by aggregating insights across multiple AWS accounts. |
| Amazon EventBridge | Emits DevOps Guru events to EventBridge for custom automation and routing workflows. |

## Artifacts

Machine-readable API specifications organized by format.

### OpenAPI

- [Amazon DevOps Guru OpenAPI](openapi/amazon-devops-guru-openapi.yaml)

### JSON Schema

- [Proactive Insight Schema](json-schema/amazon-devops-guru-proactive-insight-schema.json)
- [Reactive Insight Schema](json-schema/amazon-devops-guru-reactive-insight-schema.json)
- [Recommendation Schema](json-schema/amazon-devops-guru-recommendation-schema.json)
- [Proactive Anomaly Schema](json-schema/amazon-devops-guru-proactive-anomaly-schema.json)
- [Reactive Anomaly Schema](json-schema/amazon-devops-guru-reactive-anomaly-schema.json)
- [Resource Collection Schema](json-schema/amazon-devops-guru-resource-collection-schema.json)
- [Notification Channel Schema](json-schema/amazon-devops-guru-notification-channel-schema.json)
- [Insight Health Schema](json-schema/amazon-devops-guru-insight-health-schema.json)

### JSON Structure

- [Proactive Insight Structure](json-structure/amazon-devops-guru-proactive-insight-structure.json)
- [Reactive Insight Structure](json-structure/amazon-devops-guru-reactive-insight-structure.json)
- [Recommendation Structure](json-structure/amazon-devops-guru-recommendation-structure.json)
- [Proactive Anomaly Structure](json-structure/amazon-devops-guru-proactive-anomaly-structure.json)

### JSON-LD

- [Amazon DevOps Guru Context](json-ld/amazon-devops-guru-context.jsonld)

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [Amazon DevOps Guru API](capabilities/shared/devops-guru-api.yaml) — 18 operations for insight management, anomaly investigation, recommendations, notifications, and resource coverage

### Workflow Capabilities

| Workflow | APIs Combined | Tools | Persona |
|----------|--------------|-------|---------|
| [Operational Intelligence](capabilities/operational-intelligence.yaml) | devops-guru | 14 | DevOps Engineer, Site Reliability Engineer |

## Vocabulary

- [Amazon DevOps Guru Vocabulary](vocabulary/amazon-devops-guru-vocabulary.yaml) — Unified taxonomy mapping 10 resources, 8 actions, 1 workflow, and 2 personas across operational (OpenAPI) and capability (Naftiko) dimensions

## Rules

- [Amazon DevOps Guru Spectral Rules](rules/amazon-devops-guru-spectral-rules.yml) — 17 rules across 8 categories enforcing Amazon DevOps Guru API conventions

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
