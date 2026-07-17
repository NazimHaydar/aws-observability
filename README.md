# AWS Observability (Terraform)

A CloudWatch-based monitoring and alerting stack for an EC2 instance, built entirely with Terraform — dashboard, alarms, and email notifications, all defined as code.

## Architecture

- **EC2 instance** (t2.micro, free-tier eligible) — the monitored resource
- **Security group** — SSH access for the instance
- **CloudWatch dashboard** — visualizes CPU utilization, network in/out, disk read/write, and instance status checks
- **CloudWatch alarms**:
  - High CPU utilization
  - High network out
  - Status check failed
- **SNS topic + email subscription** — alarms publish to SNS, which sends email notifications when triggered

## Why these decisions

- **Dashboard + alarms as code**: monitoring configuration is version-controlled and reproducible, not manually clicked together in the console — so it can be recreated identically in another environment or account
- **Status check alarm**: catches instance-level failures (not just performance issues), which is often missed in basic monitoring setups
- **SNS for notifications**: a standard, decoupled way to route alerts — the same topic could later fan out to Slack, PagerDuty, or a Lambda function without changing the alarm definitions themselves
- **Separated files per concern** (`alarms.tf`, `dashboard.tf`, `sns.tf`, `variables.tf`): keeps the configuration readable and maintainable as it grows, rather than one large file

## Tech stack

Terraform · AWS (EC2, CloudWatch, SNS)

## Project structure

```
.
├── main.tf          # EC2 instance and security group
├── dashboard.tf     # CloudWatch dashboard definition
├── alarms.tf         # CloudWatch metric alarms
├── sns.tf            # SNS topic and email subscription
├── variables.tf       # Input variables
├── outputs.tf         # Output values
├── .gitignore
└── README.md
```

## How to run

```
terraform init
terraform plan
terraform apply
```

You'll need a `terraform.tfvars` file (not committed to this repo) with your alert email, e.g.:
```
alert_email = "your-email@example.com"
```

**Note**: after applying, AWS sends a confirmation email to the address in `alert_email` — the subscription must be confirmed before alarms will actually notify you.

To tear down all resources:
```
terraform destroy
```

## What I learned building this

- How to translate familiar monitoring concepts (from hands-on Zabbix administration) into AWS-native, code-defined observability
- The importance of alerting on multiple failure modes (performance *and* availability), not just a single metric like CPU
- How SNS decouples alarm triggers from notification delivery, making the alerting pipeline easier to extend later

## Author

**Nazim Haydar**
IT Support / Network Engineer transitioning into Cloud & DevOps Engineering
[GitHub](https://github.com/NazimHaydar)
