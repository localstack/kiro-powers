---
name: 'localstack'
displayName: 'Develop AWS apps with LocalStack'
description: 'Build, test, and debug AWS applications locally and in CI/CD using LocalStack. Manage the local cloud environment, deploy infrastructure with CDK/Terraform/SAM, analyze logs, enforce IAM policies, inject chaos faults, and manage state snapshots.'
keywords:
  [
    'localstack',
    'aws',
    'local',
    'cloud',
    'emulation',
    'testing',
    'lambda',
    's3',
    'dynamodb',
    'terraform',
    'cdk',
    'cloudformation',
    'iam',
    'chaos',
    'cloud-pods',
    'ephemeral',
    'mocking',
    'local-dev',
  ]
author: 'LocalStack'
---

# LocalStack Power

## Overview

LocalStack is a fully functional local cloud stack that emulates AWS services on your machine. The LocalStack Power gives you intelligent tooling to manage your local cloud environment, deploy infrastructure, debug issues, and simulate real-world failure conditions — with no cloud waste.

**Key capabilities:**

- **Container Management**: Start, stop, restart, and monitor your LocalStack instance
- **Infrastructure Deployment**: Deploy CDK, Terraform, SAM, and CloudFormation to LocalStack with zero config changes
- **AWS CLI Integration**: Execute `awslocal` commands directly inside the LocalStack container
- **Log Analysis**: Analyze LocalStack logs for errors, API call patterns, and debugging
- **IAM Policy Enforcement**: Detect permission violations and auto-generate least-privilege policies
- **Chaos Engineering**: Inject network faults and service failures to test application resilience
- **State Management**: Save and load Cloud Pod snapshots for reproducible environments and team collaboration
- **Extensions**: Install and manage LocalStack extensions from the marketplace
- **Ephemeral Instances**: Spin up temporary cloud-hosted LocalStack instances for CI/CD and demos
- **Documentation Search**: Query the official LocalStack docs directly from within Kiro

## Available Steering Files

This power includes the following steering files:

- **localstack-best-practices** - General best practices for developing against LocalStack (auto-loads for LocalStack-related files)
- **iac-deployment** - Guidance for deploying Terraform, CDK, CloudFormation, and Pulumi to LocalStack
- **state-management** - Working with Cloud Pods, local snapshots, and persistence

## Available MCP Tools

### Container Management

**`localstack-management`** - Start, stop, restart, and check the status of your LocalStack container. Injects environment variables and monitors health.

```javascript
usePower('localstack', 'localstack', 'localstack-management', {
  action: 'start',
  env: { DEBUG: '1', PERSISTENCE: '1' },
});
```

### Infrastructure Deployment

**`localstack-deployer`** - Deploy CDK, Terraform, and SAM infrastructure to LocalStack automatically using the `cdklocal`, `tflocal`, and `samlocal` wrapper tools.

```javascript
usePower('localstack', 'localstack', 'localstack-deployer', {
  tool: 'terraform',
  action: 'apply',
  directory: './infra',
});
```

### AWS CLI Execution

**`localstack-aws-client`** - Execute `awslocal` CLI commands inside the running LocalStack container. Commands are sanitized to prevent shell injection.

```javascript
usePower('localstack', 'localstack', 'localstack-aws-client', {
  command: 's3 ls',
});
```

### Log Analysis

**`localstack-logs-analysis`** - Analyze LocalStack logs for errors, API request patterns, service call metrics, and failure summaries. Supports filtering by service and operation.

```javascript
usePower('localstack', 'localstack', 'localstack-logs-analysis', {
  mode: 'errors',
  service: 'lambda',
});
```

### IAM Policy Analyzer

**`localstack-iam-policy-analyzer`** - Set IAM enforcement levels, detect permission violations, and auto-generate least-privilege IAM policies based on actual access patterns observed in logs. Requires LocalStack Pro.

```javascript
usePower('localstack', 'localstack', 'localstack-iam-policy-analyzer', {
  action: 'generate-policy',
  enforcement: 'soft',
});
```

### Chaos Engineering

**`localstack-chaos-injector`** - Inject network latency, service errors, and fault rules to simulate real-world failure conditions and test application resilience. Requires LocalStack Pro.

```javascript
usePower('localstack', 'localstack', 'localstack-chaos-injector', {
  action: 'inject',
  service: 'dynamodb',
  fault: 'error',
  region: 'us-east-1',
});
```

### Cloud Pods (State Snapshots)

**`localstack-cloud-pods`** - Save and load state snapshots (Cloud Pods) to reproduce environments, share state across team members, and preload CI/CD pipelines. Requires LocalStack Pro.

```javascript
usePower('localstack', 'localstack', 'localstack-cloud-pods', {
  action: 'save',
  name: 'my-feature-branch-state',
});
```

### Extensions

**`localstack-extensions`** - Install, list, and uninstall LocalStack Extensions from the marketplace (e.g., MailHog for SES email capture). Requires LocalStack Pro.

```javascript
usePower('localstack', 'localstack', 'localstack-extensions', {
  action: 'install',
  name: 'localstack-extension-mailhog',
});
```

### Ephemeral Instances

**`localstack-ephemeral-instances`** - Launch and manage temporary cloud-hosted LocalStack instances. Ideal for CI/CD pipelines, demos, and isolated testing environments. Requires LocalStack Pro.

```javascript
usePower('localstack', 'localstack', 'localstack-ephemeral-instances', {
  action: 'create',
  lifetime: 60,
});
```

### Documentation Search

**`localstack-docs`** - Search the official LocalStack documentation. No running LocalStack instance required.

```javascript
usePower('localstack', 'localstack', 'localstack-docs', {
  query: 'how to configure SQS with LocalStack',
});
```

---

## Onboarding

### Prerequisites

Before using the LocalStack Power, ensure you have the following installed:

1. **Docker**
   - Check: `docker --version`
   - Install: https://docs.docker.com/get-docker/

2. **LocalStack CLI**
   - Check: `localstack --version`
   - Install: `pip install localstack` or `brew install localstack/tap/localstack-cli`

3. **Node.js v22+** (required for the MCP server)
   - Check: `node --version`
   - Install: https://nodejs.org/

4. **AWS CLI + awslocal wrapper** (for running commands against LocalStack)
   - Install AWS CLI: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
   - Install awslocal: `pip install awscli-local`
   - Check: `which awslocal` (note: `awslocal --version` is not a valid command — use `which awslocal` to verify installation)

5. **IaC wrapper tools** (optional, for deploying infrastructure):
   - Terraform users: `pip install terraform-local` (provides `tflocal`)
   - CDK users: `npm install -g aws-cdk-local aws-cdk` (provides `cdklocal`)
   - SAM users: `pip install aws-sam-cli-local` (provides `samlocal`)
   - Pulumi users: `pip install pulumi-local` (provides `pulumilocal`)

### Step 1: Set Your Auth Token

A LocalStack Auth Token is required. Core emulation features can be used with a free, non-commercial-use Hobby account, but some features require a paid subscription.

Get your token at: https://app.localstack.cloud/workspace/auth-token

**Configure it in the Power's `mcp.json`:**

Open the LocalStack Power's `mcp.json` and replace `${LOCALSTACK_AUTH_TOKEN}` with your actual token value:

```json
{
  "mcpServers": {
    "localstack": {
      "command": "npx",
      "args": ["-y", "@localstack/localstack-mcp-server"],
      "env": {
        "LOCALSTACK_AUTH_TOKEN": "ls-your-actual-token-here"
      }
    }
  }
}
```

This is the only configuration needed for Kiro MCP tools to authenticate. Do **not** check for or require a shell environment variable — the token in `mcp.json` is passed directly to the MCP server and is sufficient.

> **CLI usage only:** If you also run `localstack start` or `awslocal` commands from a terminal outside of Kiro, you can additionally run `localstack auth set-token <YOUR_AUTH_TOKEN>` to set your auth token within your local environment.

### Step 2: Start LocalStack

```bash
# Basic start
localstack start -d

# Start with debug mode and persistence
DEBUG=1 PERSISTENCE=1 localstack start -d

# Verify it's running
localstack status
curl http://localhost:4566/_localstack/health | jq
```

### Step 3: Verify the MCP Server

The LocalStack MCP server is installed automatically via `npx` when Kiro starts. Verify your auth token is set as the `LOCALSTACK_AUTH_TOKEN` value in the Power's `mcp.json` (see Step 1). No shell environment variable is needed for the MCP server to work.

### Step 4: Start Building

Try these starter commands:

```
"Start LocalStack with debug mode enabled"
"Create an S3 bucket called my-test-bucket"
"List all running AWS services in LocalStack"
"Deploy my Terraform configuration to LocalStack"
"Show me any errors in the LocalStack logs"
```

---

## Common Workflows

### Local AWS Development

```
"Start LocalStack and create the S3 buckets and DynamoDB tables my app needs"
"Run my CDK app against LocalStack"
"List all Lambda functions in LocalStack"
"Invoke my process-orders Lambda function with this test event"
```

### Debugging

```
"Show me the last 50 lines of LocalStack logs"
"Are there any errors related to Lambda in the logs?"
"Why is my SQS queue not triggering my Lambda?"
"Check the health of all LocalStack services"
```

### IAM Policy Development

```
"Enable soft IAM enforcement and run my test suite, then generate a least-privilege policy"
"What IAM permissions does my app actually need based on the logs?"
"Simulate whether my role can call s3:PutObject on my-bucket"
```

### State & Collaboration

```
"Save the current LocalStack state as a Cloud Pod named 'sprint-42-demo'"
"Load the 'team-baseline' Cloud Pod so I have the shared dev environment"
"Export local state to a file before I make these destructive changes"
```

### Chaos & Resilience Testing

```
"Inject a 500ms latency on all DynamoDB calls"
"Simulate a 503 error rate of 20% on S3 GetObject"
"Clear all active chaos rules"
"Test how my app handles Lambda throttling errors"
```

### CI/CD with Ephemeral Instances

```
"Create a temporary LocalStack instance for my PR test run"
"List my active ephemeral instances"
"Delete the ephemeral instance from my last pipeline run"
```

---

## Best Practices

### Development Workflow

- **Always use wrapper tools**: Use `tflocal`, `cdklocal`, `awslocal`, and `pulumilocal` instead of their vanilla counterparts — they automatically route to LocalStack with no code changes required.
- **Enable persistence during development**: Start LocalStack with `PERSISTENCE=1` to retain state across restarts and avoid re-creating resources repeatedly.
- **Use Cloud Pods for team baselines**: Save a known-good state as a Cloud Pod so teammates can instantly load a consistent environment.
- **Test against LocalStack before AWS**: Catch configuration errors, IAM issues, and logic bugs locally before running up cloud costs.

### IAM Best Practices

- **Start with soft enforcement**: Use `ENFORCE_IAM=soft` to discover required permissions without breaking your application. Once the permission set is stable, switch to `ENFORCE_IAM=1`.
- **Generate policies from usage**: Let LocalStack observe your application's actual API calls in soft mode, then use the IAM Policy Analyzer to generate a least-privilege policy.
- **Mirror your production IAM setup**: Configure LocalStack IAM to match production roles and policies to catch permission issues before deploying.

### Debugging

- **Enable debug logging**: Start with `DEBUG=1` when troubleshooting. Use `LS_LOG=trace` for maximum verbosity.
- **Use the logs analysis tool**: Instead of grepping raw logs, use `localstack-logs-analysis` to get structured error summaries and API call metrics.
- **Check health endpoints**: `curl http://localhost:4566/_localstack/health | jq` shows which services are available and any initialization errors.

### Chaos Engineering

- **Always clean up faults**: After chaos testing, use `localstack-chaos-injector` with `action: clear` to remove all fault rules before running functional tests.
- **Test one failure mode at a time**: Inject faults for a single service or operation to isolate how your application responds.
- **Combine with Cloud Pods**: Save a known-good state before injecting chaos so you can quickly restore it after testing.

---

## Troubleshooting

### LocalStack Won't Start

```bash
# Check if port 4566 is already in use
lsof -i :4566

# Check Docker is running
docker info

# View startup logs
localstack start 2>&1 | head -50
```

### MCP Server Not Connecting

- Verify `LOCALSTACK_AUTH_TOKEN` is set to your actual token value in the Power's `mcp.json` (not the `${LOCALSTACK_AUTH_TOKEN}` placeholder)
- Ensure Node.js v22+ is installed: `node --version`
- Try running manually: `LOCALSTACK_AUTH_TOKEN=your-token npx -y @localstack/localstack-mcp-server`

### Services Not Available

```bash
# Check service health
curl http://localhost:4566/_localstack/health | jq '.services'

# Restart with debug mode
localstack stop && DEBUG=1 localstack start -d
```

### awslocal Command Not Found

```bash
pip install awscli-local
# Verify installation (use 'which', not '--version' — awslocal has no version flag)
which awslocal
```

If `which awslocal` returns a path, the tool is installed. The setup check `awslocal --version` will fail even when `awslocal` is correctly installed — use `which awslocal` instead.

### Pro/Emulator Enhancement Features Not Working

- Verify your auth token is valid at https://app.localstack.cloud/
- Confirm the token is set directly in the `env` block of the Power's `mcp.json`, not left as the `${LOCALSTACK_AUTH_TOKEN}` placeholder
- Ensure your subscription tier includes the feature you're trying to use

---

## Learn More

- LocalStack Documentation: https://docs.localstack.cloud
- LocalStack GitHub: https://github.com/localstack/localstack
- LocalStack MCP Server: https://github.com/localstack/localstack-mcp-server
- Community Slack: https://localstack.cloud/slack
- LocalStack Extensions: https://docs.localstack.cloud/user-guide/extensions/
- Cloud Pods Guide: https://docs.localstack.cloud/user-guide/state-management/cloud-pods/
