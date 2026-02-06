<!-- SPDX-FileCopyrightText: Copyright (c) 2026 NVIDIA CORPORATION & AFFILIATES. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

SPDX-License-Identifier: Apache-2.0 -->

<a id="access-tokens"></a>

# Personal Access Tokens

Personal Access Tokens (PATs) provide a way to authenticate with OSMO programmatically,
enabling integration with CI/CD pipelines, scripts, and automation tools.

## Overview

PATs are tied to your user account and inherit your roles at creation time. When you create
a PAT, it receives either all of your current roles or a subset that you specify.

> **Important**
>
> - PAT roles are immutable after creation. To change a token’s roles, delete the token and create a new one.
> - When a role is removed from your user account, it is automatically removed from all your PATs.
> - Store your PAT securely—it is only displayed once at creation time.

## Creating a Personal Access Token

### Using the CLI

1. First, log in to OSMO:
   ```bash
   $ osmo login https://osmo.example.com
   ```
2. Create a new token with an expiration date:
   ```bash
   $ osmo token set my-token --expires-at 2027-01-01 --description "My automation token"
   ```

   The token will be displayed once. Save it securely.

   **Example output:**
   ```text
   Note: Save the token in a secure location as it will not be shown again
   Access token: osmo_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   ```
3. (Optional) Verify the token was created:
   ```bash
   $ osmo token list
   ```

#### CLI Options

| Option              | Required   | Description                                                         |
|---------------------|------------|---------------------------------------------------------------------|
| `--expires-at, -e`  | No         | Expiration date in YYYY-MM-DD format. Defaults to 31 days from now. |
| `--description, -d` | No         | A description to help identify the token’s purpose.                 |
| `--format-type, -t` | No         | Output format: `text` (default) or `json`.                          |

## Using a Personal Access Token

Once you have a PAT, you can use it to authenticate with OSMO.

### CLI Authentication

Log in using the token method:

```bash
$ osmo login https://osmo.example.com --method=token --token=osmo_xxxxxxxxxx
```

After logging in, all subsequent CLI commands will use this authentication:

```bash
$ osmo workflow list
$ osmo workflow submit my-workflow.yaml
```

Alternatively, you can store the token in a file and reference it:

```bash
# Store token in a file (ensure proper file permissions)
$ echo "osmo_xxxxxxxxxx" > ~/.osmo-token
$ chmod 600 ~/.osmo-token

# Login using the token file
$ osmo login https://osmo.example.com --method=token --token-file=~/.osmo-token
```

> **Note**
>
> The `--method=token` login exchanges your PAT for a short-lived JWT that is used
> for subsequent API calls. This JWT is automatically refreshed as needed.

## Managing Tokens

### List Your Tokens

```bash
$ osmo token list
```

**Example output:**

```text
+---------------+-------------------------+--------+------------------+
| Name          | Description             | Active | Expires At (UTC) |
+---------------+-------------------------+--------+------------------+
| my-token      | My automation token     | Active | 2027-01-01       |
| ci-token      | CI/CD pipeline          | Active | 2026-12-31       |
+---------------+-------------------------+--------+------------------+
```

### View Token Roles

To see which roles are assigned to a specific token:

```bash
$ osmo token roles my-token
```

**Example output:**

```text
Token: my-token
Owner: user@example.com

Roles:
  - osmo-user (assigned by admin@example.com on 2026-01-15)
  - osmo-ml-team (assigned by admin@example.com on 2026-01-15)
```

### Delete a Token

```bash
$ osmo token delete my-token
```

## Best Practices

Set Appropriate Expiration

Always set an expiration date appropriate for your use case. For CI/CD pipelines,
consider shorter expiration periods and rotate tokens regularly.

Use Descriptive Names

Use descriptive token names and descriptions to help identify their purpose
(e.g., `ci-github-actions`, `jenkins-prod-pipeline`).

Secure Storage

Store tokens in secure secret management systems like HashiCorp Vault,
AWS Secrets Manager, or Kubernetes Secrets.

Rotate Regularly

Periodically rotate tokens by creating a new token and deleting the old one.
This limits the impact of potential token compromise.

#### SEE ALSO
- [tocdepth](../appendix/cli/cli_token.md#cli-reference-token) for full CLI reference
