# Set up Kortix: managed cloud or self-hosted, with your own keys

Kortix is the open-source AI Operating System, and this page is its setup path: the managed cloud, three ways to self-host, the configuration each one asks for, and the first session through to the first reviewed change. Kortix is open source, so the code behind the install is public: you can read it, fork it and audit it, and self-hosting is free. Every step links to the project's own documentation, so what you read here matches what the project ships. If you are coming from AnythingLLM, the hosting comparison in the [self-hosting guide](https://anythingllm-alternative.com/self-hosting-anythingllm-alternative.html) on anythingllm-alternative.com covers how this install differs from an AnythingLLM Docker deployment before you commit.

## What you need before you start

- Nothing but a browser for the managed cloud path.
- A Linux box you control for self-hosting: a VPS, a machine in your VPC, or an on-prem host. A laptop works for evaluation ([self-hosting docs](https://kortix.com/docs/host)).
- A model to run the agents: any provider's API key, your own OpenAI-compatible endpoint, or the ChatGPT subscription you already pay for ([kortix.com](https://kortix.com)).

## Way 1: managed cloud, nothing to install

1. Sign up at [kortix.com](https://kortix.com).
2. Create a project and start a session. The project is a git repo: agents, skills, memory, connectors and triggers live in it as files.
3. Connect a model in the model picker: your own API key for any provider, or your ChatGPT subscription.

The Free plan is $0 with 200 credits per month for sandbox compute and 1 project, and you can bring your own API key for any premium model ([kortix.com/pricing](https://kortix.com/pricing), quoted as written, checked October 2026). Judge the change-request loop here before you host anything yourself.

## Way 2: self-host, one-shot bootstrap

On a bare Linux box, the docs give a one-shot bootstrap script: one command installs Docker, installs the `kortix` CLI, and starts the whole stack, taking your domain and an email address as arguments. The script runs on Linux only; on another operating system, use the manual path below. The command itself lives in the [self-hosting docs](https://kortix.com/docs/host).

## Way 3: self-host, manual path

1. Install the CLI:

   ```bash
   curl -fsSL https://kortix.com/install | bash
   ```

2. Point DNS at the box: create an A or AAAA record for your domain and for `api.<domain>`, both pointing at the machine's IP. Open ports 80 and 443, which the bundled Caddy proxy uses to issue TLS certificates.
3. Initialize with your domain and start the stack:

   ```bash
   kortix self-host init --domain kortix.example.com
   kortix self-host start
   ```

4. Watch it come up with `kortix self-host status`, `kortix self-host logs`, and `kortix self-host doctor` ([self-hosting docs](https://kortix.com/docs/host)).

The stack itself is one Docker Compose deployment: the frontend, the API, the LLM gateway, and the Supabase distribution. Note the boundary the docs draw: sessions do not run on this stack. Each session runs on a separate sandbox provider, as its own isolated sandbox ([self-hosting docs](https://kortix.com/docs/host)).

## Way 4: evaluation mode, no domain

To try Kortix without owning a domain, run it behind a Cloudflare tunnel:

```bash
kortix self-host init --tunnel cloudflare
kortix self-host start
```

The tunnel URL changes on every restart, so treat this as a trial run rather than production ([self-hosting docs](https://kortix.com/docs/host)).

## Configure the stack, then connect a model

Once the stack is up, run `kortix self-host configure`. It is an interactive prompt for the sandbox provider key and, optionally, a managed-git token. Platinum is the default sandbox provider, with Daytona available as a legacy fallback ([self-hosting docs](https://kortix.com/docs/host)).

Then sign up in the dashboard and connect your model key in the model picker. Self-hosted instances use your own key by default: the setup prompt asks for the sandbox provider key, not a model key, so models stay yours. GitHub connects afterward, in the dashboard under Settings → Git ([self-hosting docs](https://kortix.com/docs/host)).

Sizing note from the same page: the API service has a 640 MiB memory limit by default, which the docs say to keep on an 8 GiB host. On a 16 GiB host under API load, raise it to 1 GiB with `kortix self-host env set KORTIX_API_MEMORY_LIMIT=1024m` and confirm with `docker stats --no-stream`.

## Ship your first work

With the CLI installed (managed cloud or self-hosted), the workflow is the same, and it treats your company like a codebase:

```bash
kortix init     # scaffold kortix.yaml plus agents, skills and runtime config
kortix ship     # push the repo and bring the project live
kortix sessions new --prompt "Summarize this week's commits and open a change request"
kortix cr ls    # review what the agent proposes
```

A session runs an agent in its own sandbox, on its own branch named after the session. When the work is ready, the agent opens a change request; you read the diff and merge it to the default branch ([the docs](https://kortix.com/docs), [Kortix on GitHub](https://github.com/kortix-ai/suna)). Nothing merges itself: that review gate is the default, not an option you have to find.

## Operating it day to day

- **Updates:** every instance updates itself automatically. Pin an exact version with `kortix self-host update --tag 0.9.84`, or turn the updater off with `--auto-update off` ([self-hosting docs](https://kortix.com/docs/host)).
- **Backups:** there is no separate backup system. Back up three things: the Postgres data directory and the file storage directory under `~/.config/kortix/self-host/<instance>/`, plus the instance `.env` file that holds every secret and signing key ([self-hosting docs](https://kortix.com/docs/host)).
- **Network:** `kortix self-host start` pulls its images from Docker Hub, so the install is self-hosted rather than disconnected, in the docs' own words. The instance must stay reachable so sessions on the sandbox provider can call back ([Kortix on GitHub](https://github.com/kortix-ai/suna)).

That is the whole path: one install, one model key, and a repo you own from the first commit. The code behind all of it is [Kortix on GitHub](https://github.com/kortix-ai/suna), and if you would rather start managed and self-host later, [Try Kortix](https://kortix.com) in the cloud.
