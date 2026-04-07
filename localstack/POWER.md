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

LocalStack is a fully functional local cloud stack that emulates AWS services on your machine. **Every developer must use a LocalStack auth token** (including free Hobby accounts); **your plan tier** determines which product features and MCP tools you can use. The LocalStack Power gives you intelligent tooling to manage your local cloud environment, deploy infrastructure, debug issues, and simulate real-world failure conditions — with no cloud waste.

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

Load the right steering file for the task so guidance stays focused and MCP-backed workflows stay front and center:

- **localstack-best-practices** — Day-to-day LocalStack development, endpoints, health checks, and when to use MCP tools (`localstack-management`, `localstack-logs-analysis`, `localstack-aws-client`, `localstack-docs`) instead of ad hoc shell commands.
- **iac-deployment** — Deploying with Terraform, CDK, SAM, CloudFormation, or Pulumi; prefers **`localstack-deployer`** MCP where appropriate, with `tflocal` / `cdklocal` / etc. as the terminal fallback.
- **state-management** — Persistence, local state export/import, and Cloud Pods; uses **`localstack-cloud-pods`** MCP for Cloud Pod workflows alongside CLI patterns (where your plan tier allows).

## Available MCP Tools

### Container Management

**`localstack-management`** - Start, stop, restart, and check the status of your LocalStack container. Injects environment variables and monitors health.

```javascript
usePower('localstack', 'localstack', 'localstack-management', {
  action: 'start',
  envVars: { DEBUG: '1', PERSISTENCE: '1' },
});
```

### Infrastructure Deployment

**`localstack-deployer`** - Deploy CDK, Terraform, and SAM infrastructure to LocalStack automatically using the `cdklocal`, `tflocal`, and `samlocal` wrapper tools.

```javascript
usePower('localstack', 'localstack', 'localstack-deployer', {
  action: 'deploy',
  projectType: 'terraform',
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
  analysisType: 'errors',
  service: 'lambda',
});
```

### IAM Policy Analyzer

**`localstack-iam-policy-analyzer`** - Set IAM enforcement levels, detect permission violations, and auto-generate least-privilege IAM policies based on actual access patterns observed in logs. **Plan tier:** may require a higher tier than Hobby—confirm in your LocalStack workspace.

```javascript
usePower('localstack', 'localstack', 'localstack-iam-policy-analyzer', {
  action: 'set-mode',
  mode: 'SOFT_MODE',
});
```

```javascript
usePower('localstack', 'localstack', 'localstack-iam-policy-analyzer', {
  action: 'analyze-policies',
});
```

### Chaos Engineering

**`localstack-chaos-injector`** - Inject network latency, service errors, and fault rules to simulate real-world failure conditions and test application resilience. **Plan tier:** may require a higher tier than Hobby—confirm in your LocalStack workspace.

```javascript
usePower('localstack', 'localstack', 'localstack-chaos-injector', {
  action: 'inject-latency',
  latency_ms: 500,
});
```

```javascript
usePower('localstack', 'localstack', 'localstack-chaos-injector', {
  action: 'inject-faults',
  rules: [
    {
      service: 'dynamodb',
      region: 'us-east-1',
      operation: 'PutItem',
      probability: 0.2,
      error: { statusCode: 503, code: 'ServiceUnavailable' },
    },
  ],
});
```

### Cloud Pods (State Snapshots)

**`localstack-cloud-pods`** - Save and load state snapshots (Cloud Pods) to reproduce environments, share state across team members, and preload CI/CD pipelines. **Plan tier:** Cloud Pods often require a paid or team tier—confirm in your LocalStack workspace.

```javascript
usePower('localstack', 'localstack', 'localstack-cloud-pods', {
  action: 'save',
  pod_name: 'my-feature-branch-state',
});
```

### Extensions

**`localstack-extensions`** - Install, list, and uninstall LocalStack Extensions from the marketplace (e.g., MailHog for SES email capture). **Plan tier:** may require a higher tier than Hobby—confirm in your LocalStack workspace.

```javascript
usePower('localstack', 'localstack', 'localstack-extensions', {
  action: 'install',
  name: 'localstack-extension-mailhog',
});
```

### Ephemeral Instances

**`localstack-ephemeral-instances`** - Launch and manage temporary cloud-hosted LocalStack instances. Ideal for CI/CD pipelines, demos, and isolated testing environments. **Plan tier:** may require a higher tier than Hobby—confirm in your LocalStack workspace.

```javascript
usePower('localstack', 'localstack', 'localstack-ephemeral-instances', {
  action: 'create',
  name: 'ci-preview-1',
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

### MCP-first workflows

When helping in Kiro, **prefer MCP tools** over unstructured shell greps so behavior matches what the user configured in `mcp.json` and stays consistent with [LocalStack MCP Server](https://github.com/localstack/localstack-mcp-server) capabilities:

| Goal                                    | Prefer                           | Instead of (when possible)                                    |
| --------------------------------------- | -------------------------------- | ------------------------------------------------------------- |
| Start/stop/restart or health            | `localstack-management`          | Raw `docker` commands without context                         |
| Deploy CDK/Terraform/SAM                | `localstack-deployer`            | Manual `tflocal`/`cdklocal` in chat without project detection |
| Read or summarize logs                  | `localstack-logs-analysis`       | Pasting huge `localstack logs` output                         |
| Run AWS API calls against LocalStack    | `localstack-aws-client`          | Unsanitized `aws` against real AWS endpoints                  |
| IAM violations / least-privilege drafts | `localstack-iam-policy-analyzer` | Hand-written IAM from guesses                                 |
| Cloud Pods snapshots                    | `localstack-cloud-pods`          | Only CLI `localstack pod` when MCP fits                       |
| Chaos / faults                          | `localstack-chaos-injector`      | Ad hoc failure injection scripts                              |
| Docs and coverage questions             | `localstack-docs`                | Generic web search only                                       |

Tools that depend on your subscription tier still require a valid auth token and the right plan for that capability; **`localstack-docs`** is the lightest check that the MCP server is up and authenticated.

---

## Onboarding

### Try Power, health checks, and auth (read this first)

**The LocalStack MCP server receives its auth token from the Power's MCP configuration** (the `env.LOCALSTACK_AUTH_TOKEN` entry in `mcp.json`, or a secret placeholder your IDE resolves when launching MCP). That value is **not** automatically visible in an ordinary interactive terminal session.

**Do not** use any of the following to decide whether setup succeeded:

- `echo $LOCALSTACK_AUTH_TOKEN`, `printenv LOCALSTACK_AUTH_TOKEN`, or `[[ -n "$LOCALSTACK_AUTH_TOKEN" ]]`
- Requiring the user to export the token in the shell for Kiro MCP to work

**Do** verify setup as follows:

1. **MCP server and token (Kiro):** Invoke the **`localstack-docs`** tool with a short query such as `LocalStack Docker install`. This checks that the MCP package runs and accepts the configured token. It does **not** require LocalStack to be running. If you need runtime status, use **`localstack-management`** with `action: status` after Docker is up.
2. **CLI and `localstack start`:** Configure the token with the LocalStack CLI (persistent on the machine), not by insisting on a shell env var for day-to-day use:
   ```bash
   localstack auth set-token <YOUR_AUTH_TOKEN>
   ```
   Confirm configuration without printing the full secret (masked output is OK):
   ```bash
   localstack auth show-token
   ```
   Expect `Valid: True` when a token is configured. Use `localstack auth show-token --plain` only when piping to another program, not for casual verification.

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

### Step 1: Configure your auth token

**Every user** must configure a LocalStack Auth Token—including free Hobby accounts. The MCP server and CLI both rely on it. **Which features you can use** (including some MCP tools) depends on your plan tier; see https://app.localstack.cloud/workspace/auth-token

**For Kiro MCP tools:** Ensure `mcp.json` passes a real token to the MCP process. You may either substitute the literal token in `env.LOCALSTACK_AUTH_TOKEN` or keep a placeholder if your environment injects secrets when Kiro starts the server:

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

**For terminal use** (`localstack start`, `awslocal`, Cloud Pods CLI, etc.): run **`localstack auth set-token <YOUR_AUTH_TOKEN>`** once per machine or CI image. The CLI stores credentials for the LocalStack container; you do not need to keep `LOCALSTACK_AUTH_TOKEN` in your shell profile for normal use.

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

### Step 3: Confirm MCP tools (not shell env vars)

After MCP is configured in `mcp.json`, confirm behavior by **calling MCP tools** — see _Try Power, health checks, and auth_ above. Do not re-check authentication by inspecting the user's shell environment.

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

- **Always clean up faults**: After chaos testing, use `localstack-chaos-injector` with `action: 'clear-all-faults'` (and `clear-latency` if you injected latency) before running functional tests.
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

- Confirm the token in the Power's `mcp.json` is a real value (or that your IDE resolves `${LOCALSTACK_AUTH_TOKEN}` when launching MCP). Do not rely on the interactive shell having the variable set.
- Ensure Node.js v22+ is installed: `node --version`
- From a terminal, you can smoke-test the package with stdin closed only if you also pass the token for that one process — for example: `LOCALSTACK_AUTH_TOKEN=your-token npx -y @localstack/localstack-mcp-server` — but **prefer verifying from inside Kiro** by calling **`localstack-docs`** once the Power is loaded.

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

### Feature or MCP Tool Not Available

- Verify your auth token is valid at https://app.localstack.cloud/
- For Kiro: confirm the MCP `env.LOCALSTACK_AUTH_TOKEN` value in `mcp.json` (or secret injection) is correct — use an MCP tool call to confirm, not `echo` in bash.
- For CLI: run `localstack auth show-token` and confirm `Valid: True`
- Confirm your **plan tier** includes the product feature or MCP capability you are using (some tools need a higher tier than Hobby)

---

## Learn More

- LocalStack Documentation: https://docs.localstack.cloud
- LocalStack GitHub: https://github.com/localstack/localstack
- LocalStack MCP Server: https://github.com/localstack/localstack-mcp-server
- Community Slack: https://localstack.cloud/slack
- LocalStack Extensions: https://docs.localstack.cloud/user-guide/extensions/
- Cloud Pods Guide: https://docs.localstack.cloud/user-guide/state-management/cloud-pods/
