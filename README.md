<div align="center">
  <a href="https://secura.ink">
    <img src="https://secura.ink/favicon.ico" alt="Secura Logo" width="84" height="84" />
  </a>

  # Secura — Code Security & Secret Gate

  **Cut Code Review Time. Stop Secret Leaks Before Merge.**

  **[🌐 Website: secura.ink](https://secura.ink)** &nbsp;•&nbsp; **[⚡ GitHub App: secura-security-scanner](https://github.com/apps/secura-security-scanner)**

  <br />

  [![Install GitHub App](https://img.shields.io/badge/Install_GitHub_App-secura--security--scanner-0F172A?style=for-the-badge&logo=github&logoColor=38BDF8&labelColor=0F172A&color=1E293B)](https://github.com/apps/secura-security-scanner)
  [![Website](https://img.shields.io/badge/Website-secura.ink-0F172A?style=for-the-badge&logo=googlechrome&logoColor=38BDF8&labelColor=0F172A&color=1E293B)](https://secura.ink)
  [![Zero Retention](https://img.shields.io/badge/Zero_Retention-0%25_Code_Stored-0F172A?style=for-the-badge&logo=shield&logoColor=10B981&labelColor=0F172A&color=064E3B)](#zero-code-retention)
</div>

---

## Overview

**Secura** ([secura.ink](https://secura.ink)) is an autonomous pull request security co-pilot and pre-merge secret prevention gate built natively for GitHub. Engineered for fast-moving development teams, Secura connects to public and private repositories in under 30 seconds to inspect every pull request diff for exposed credentials, API keys, private cryptographic certificates, and database connection strings—blocking leaks before they ever touch production branches.

Unlike legacy static analysis tools that clone repositories to external servers or slow down CI pipelines by 5–12 minutes, Secura evaluates diffs **ephemerally in-memory in less than 50ms** with **0% source code retention** and **99.4% less false-positive noise**.

### Key Telemetry & Benchmarks
- **500K+** Pull Requests inspected
- **100+** Active high-entropy secret & token signatures
- **< 50ms** In-memory AST scan latency (**< 1.2s** average end-to-end GitHub Check run)
- **0%** Source code retained on disk or used for model training

---

## Deep Code Intelligence: The Secura Context Engine

Traditional regex-based scanners blindly match string patterns across files, flooding pull requests with false alarms on mock fixtures, comments, and documentation examples. Secura's **Context Engine** pulls dozens of structural and mathematical signals across four layers:

1. **In-Memory AST & Tree-Sitter Parsing:**
   Secura decomposes git diff chunks into Abstract Syntax Trees across **12+ programming languages**. By understanding syntax nodes, it distinguishes between actual variable assignments, environment lookups (`process.env.STRIPE_SECRET_KEY`), imports, comments, and test fixtures with surgical precision.
2. **Shannon Entropy Noise Filter:**
   Cryptographic keys and production tokens exhibit high character randomness. Secura computes character distribution variance (Shannon entropy) alongside pattern matching to separate genuine leaked secrets from low-entropy placeholders (`YOUR_KEY_HERE`, `dummy_mock_token_123`), eliminating **99.4% of spurious alerts**.
3. **Git Chunk Boundary Inspection:**
   Instead of scanning entire historical codebases on every commit, Secura isolates exact added/modified hunk boundaries in the PR diff, keeping gate checks fast even on massive monorepos.
4. **Granular Branch & Gate Policies:**
   Configure custom gate thresholds per repository—enforcing zero-tolerance merge locks on `main` or `production` branches while keeping feature and staging workflows frictionless.

---

## Automated PR Merge Gate & Live Remediation

When a pull request is opened or updated, Secura operates directly inside the native GitHub PR interface:

1. **Webhook & TLS 1.3 Ingestion:** GitHub sends the PR diff payload over an encrypted TLS 1.3 channel.
2. **Ephemeral Inspection:** The Context Engine inspects the diff in RAM and immediately discards the payload once evaluation completes.
3. **Real-Time Merge Lock:** If a critical secret is detected, `secura-bot` registers a failed **GitHub Check Run**, locks the PR merge button, and leaves an inline review comment pinpointing the exact file, line number, and credential type.
4. **Actionable Remediation Advice:** Each alert includes tailored step-by-step fix instructions (e.g., revoking the compromised key in the provider dashboard, migrating the value to **GitHub Repository Secrets**, and referencing it via environment variables).
5. **Zero-Touch Auto-Unblock:** Once the secret is removed and a clean commit is pushed, Secura re-evaluates the diff and automatically transitions the Check Run to **Passing**, unlocking the merge button without manual overrides.

---

## Comprehensive Detection Engine (100+ Signatures)

Secura guards repositories across cloud providers, payment processors, AI/LLM platforms, databases, version control systems, and cryptographic key formats:

| Category | Detected Credentials & Sample Patterns |
| :--- | :--- |
| **Payment Gateways** | Stripe live & restricted keys (`sk_live_*`, `rk_live_*`), Square production tokens (`sq0csp-*`), PayPal (`access_token$production`), Braintree authorization keys |
| **Cloud Infrastructure** | AWS Access Key IDs (`AKIA[0-9A-Z]{16}`) & Secret Keys, Google Cloud Service Account JSONs (`gcp-service-account.json`), Azure connection strings (`AccountKey=*`), DigitalOcean PATs (`dop_v1_*`) |
| **Model & LLM Services** | OpenAI project API keys (`sk-proj-*`), Anthropic Claude keys (`sk-ant-api03-*`), HuggingFace user tokens (`hf_[a-zA-Z0-9]{34}`), Cohere keys, Replicate tokens (`r8_*`) |
| **Data Stores & Storage** | PostgreSQL (`postgresql://...`), MongoDB (`mongodb+srv://...`), Redis (`redis-cache://...`), and MySQL (`mysql://...`) connection URIs with embedded credentials |
| **VCS & Auth Tokens** | GitHub Personal Access Tokens classic & fine-grained (`ghp_*`, `github_pat_*`), GitHub OAuth secrets, GitLab tokens (`glpat-*`), npm publish tokens (`npm_*`), Slack webhooks |
| **Cryptographic Keys** | Private RSA (`BEGIN RSA PRIVATE KEY`), OpenSSH (`BEGIN OPENSSH PRIVATE`), PGP (`BEGIN PGP PRIVATE`), EC (`BEGIN EC PRIVATE`), and SSL/TLS certificates |

---

## Secura vs. Legacy Security Scanners

| Feature | Secura | Traditional Scanners |
| :--- | :--- | :--- |
| **Scan Latency** | **< 50ms** In-Memory AST (`< 1.2s` avg gate check) | 5 to 12 minutes (heavy CI runner queue & build wait) |
| **Code Retention Policy** | **0%** (Inspected in-memory, never persisted) | Clones full repository onto third-party vendor disks |
| **Test Mock Handling** | **Shannon Entropy + AST** (Zero false positives) | Spams pull requests with false alerts on test fixtures |
| **PR Gate Enforcement** | Native GitHub Check Run merge lock & auto-unblock | Passive email alerts or ignored external dashboards |
| **Setup Friction** | **30-Second 1-Click GitHub App** (Zero YAML) | Requires custom Docker images, YAML workflows, and CI secrets |

---

## Getting Started

1. Visit the official **[Secura Security Scanner GitHub App](https://github.com/apps/secura-security-scanner)** page (or sign in via **[secura.ink](https://secura.ink)**).
2. Click **Install** and grant access to **All repositories** or **Only select repositories** (supports both public and private repos).
3. Open or update any pull request—Secura will immediately begin protecting your codebase with automated pre-merge security gates.

---

## Security, Privacy & Compliance

Secura is built from the ground up to satisfy strict DevSecOps and enterprise compliance standards:

- **Zero Code Retention Policy:** Your source code is never written to disk, logged, indexed, or used to train machine learning models.
- **OWASP A02 (Cryptographic Failures):** Prevents accidental exposure of sensitive keys, tokens, and certificates in version control.
- **CWE-798 (Use of Hard-coded Credentials):** Blocks hard-coded secrets at the pull request boundary before commit history is polluted.
- **SOC 2 Ready & TLS 1.3:** Ephemeral memory processing paired with end-to-end TLS 1.3 encryption in transit.

---

## Frequently Asked Questions

- **Does Secura store or retain our source code?**
  No. Pull request diffs are streamed into memory, scanned in milliseconds, and immediately purged. Zero source code is ever saved to persistent storage.
- **How long does setup take?**
  Roughly 30 seconds. There are no `.yml` workflow files to configure, no CLI binaries to maintain, and no CI runner minutes consumed.
- **Is Secura free for individual developers and open-source?**
  Yes—Secura offers free protection for both public and private repositories so individual developers and teams can start securing PRs immediately.

---

## Links & Resources

- **Platform & Live Demo:** [secura.ink](https://secura.ink)
- **GitHub App Installation:** [github.com/apps/secura-security-scanner](https://github.com/apps/secura-security-scanner)
- **Author:** [Jay Patel](https://jaypatel.online) ([@jay240322](https://github.com/jay240322))
