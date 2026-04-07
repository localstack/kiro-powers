# LocalStack State Management

Guidelines for managing LocalStack state with local snapshots, Cloud Pods, and persistence.

## Overview

LocalStack provides three mechanisms for state management:

| Mechanism | Storage | Requires Pro | Best For |
|-----------|---------|--------------|----------|
| Local Persistence (`PERSISTENCE=1`) | Local disk | No | Simple dev-loop state retention |
| Local Snapshots (`state export/import`) | Local files | No | CI/CD, backups, version control |
| Cloud Pods (`pod save/load`) | LocalStack cloud | Yes | Team sharing, cross-machine access |

---

## Local Persistence

Enable automatic state retention across LocalStack restarts with no extra steps:

```bash
# Start with persistence enabled
PERSISTENCE=1 localstack start -d

# State is stored in .localstack/ in the current directory
# All resources survive container stop/start
```

**Use when**: You want resources to automatically survive `localstack restart` during development.

---

## Local Snapshots (State Export/Import)

Export and import LocalStack state to/from local zip files. Works without a Pro subscription.

### Exporting State

```bash
# Export current state to a file
localstack state export my-state.zip

# Export to a specific path
localstack state export /path/to/backups/state-$(date +%Y%m%d).zip
```

### Importing State

```bash
# Import state from a file (replaces current state)
localstack state import my-state.zip

# Import from a specific path
localstack state import /path/to/backups/state-20250101.zip
```

### Use Cases for Local Snapshots

- **CI/CD pipelines**: Commit state files to the repo so tests always start from a known baseline
- **Pre-destructive backups**: Export before running `tflocal destroy` or mass deletes
- **Offline development**: Share state files via Git, email, or USB when cloud connectivity isn't available

---

## Cloud Pods (Requires LocalStack Pro)

Cloud Pods store state in LocalStack's cloud platform for team collaboration and remote access.

### Prerequisites

```bash
export LOCALSTACK_AUTH_TOKEN=<your-pro-token>
```

### Saving State

```bash
# Save current state to a named Cloud Pod
localstack pod save my-pod-name

# Save with a descriptive message
localstack pod save sprint-42-demo --message "Demo state: S3, DynamoDB, and Lambda configured"
```

### Loading State

```bash
# Load state from a Cloud Pod (replaces current state)
localstack pod load my-pod-name

# Load and merge with existing state (preserves current resources)
localstack pod load my-pod-name --merge
```

### Managing Cloud Pods

```bash
# List all available Cloud Pods
localstack pod list

# View details of a specific Cloud Pod
localstack pod inspect my-pod-name

# Delete a Cloud Pod
localstack pod delete my-pod-name
```

### Cloud Pod Use Cases

- **Team baseline environments**: Save a configured state that all developers load when starting work
- **Feature branch isolation**: Each branch saves its own Cloud Pod (`pod save feature-auth-rework`)
- **Demo preparation**: Prepare a demo-ready state and load it on any machine before presenting
- **Cross-machine development**: Access the same state from your laptop and CI/CD runner

---

## Recommended Workflows

### Individual Developer (No Pro Required)

```bash
# Start with persistence for basic state retention
PERSISTENCE=1 localstack start -d

# Deploy your infrastructure
tflocal apply -auto-approve

# Save a snapshot before risky operations
localstack state export before-migration.zip

# After testing, restore if needed
localstack state import before-migration.zip
```

### Team Collaboration (Pro Required)

```bash
# Team lead sets up baseline environment
localstack start -d
# ... creates resources, deploys infrastructure ...
localstack pod save team-dev-baseline --message "S3 buckets, DynamoDB tables, Lambda functions for sprint 42"

# Each developer loads the shared baseline
export LOCALSTACK_AUTH_TOKEN=<token>
localstack start -d
localstack pod load team-dev-baseline

# Developer saves their branch state before sharing
localstack pod save feature-user-auth --message "User auth Lambda + DynamoDB table"
```

### CI/CD Pipeline

```bash
# Option 1: Use exported state file (committed to repo)
localstack start -d
localstack state import ./ci/baseline-state.zip
# Run tests
pytest tests/

# Option 2: Load a Cloud Pod (Pro, no state file in repo)
LOCALSTACK_AUTH_TOKEN=$TOKEN localstack start -d
localstack pod load ci-test-baseline
# Run tests
pytest tests/
```

---

## Best Practices

- **ALWAYS** use descriptive names for Cloud Pods and snapshot files: `feature-auth-sprint42` not `state1`
- **ALWAYS** add messages to Cloud Pod saves: `--message "What resources are in this state"`
- **Use `state export` before destructive operations**: Create a checkpoint you can restore from
- **Commit baseline state files to version control** for reproducible CI/CD pipelines
- **Use Cloud Pods for team collaboration**, local snapshots for personal backups
- **Combine with `PERSISTENCE=1`** during active development to avoid accidentally losing state on restarts
- **Keep Cloud Pods current**: Delete outdated pods to avoid confusion (`localstack pod list` then `localstack pod delete old-pod-name`)
