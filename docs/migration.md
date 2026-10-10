# Migrate from AnythingLLM to Kortix

Moving from AnythingLLM to Kortix changes where your setup lives. In AnythingLLM, the state you care about sits inside the app: documents in workspaces, configuration chosen in the interface, agent behavior defined per instance. In Kortix, the open-source AI Operating System, all of it becomes files in a git repo you own: agents, skills, company memory and connector configuration, as text you can read, diff and roll back. This page covers what carries over, what stays behind, and the order to do the work in. The product-level version of the same move is the [migration guide](https://anythingllm-alternative.com/migrate-from-anythingllm.html) on anythingllm-alternative.com.

Both products are open source, so nothing about the move is locked in: AnythingLLM's code is MIT licensed ([repository README](https://github.com/Mintplex-Labs/anything-llm)), and Kortix's is public at [Kortix on GitHub](https://github.com/kortix-ai/suna). You can run them side by side while you migrate.

## What carries over

**Your documents.** AnythingLLM ingests PDF, TXT, DOCX and other formats into workspaces ([repository README](https://github.com/Mintplex-Labs/anything-llm)). Those source files are portable by definition: copy them into the Kortix repo and agents work over them directly.

**Your models.** Whatever you connect in AnythingLLM today, local runtimes such as Ollama, LM Studio and LocalAI, or cloud providers such as OpenAI and Anthropic, keeps working on the other side. Kortix takes any model provider with your own API keys, runs your own models behind your own URL when they speak the OpenAI-compatible API, and can sign in with the ChatGPT subscription you already pay for ([kortix.com](https://kortix.com)). Only the configuration surface changes, from the app's settings to the repo.

**Your agent intent.** The behavior you configured in AnythingLLM agents translates into Kortix agents written as markdown files, with each agent's tool reach recorded in `kortix.yaml`. The work is writing down what the app was holding for you.

## What stays behind

AnythingLLM's derived state does not move, and it does not need to. The embeddings in its vector store, LanceDB by default with PGVector, Pinecone, Qdrant and others as options ([repository README](https://github.com/Mintplex-Labs/anything-llm)), are regenerated from the source files you copied. Chat history is app state; going forward, anything a session should remember long-term belongs in Kortix's `memory/` directory, where every future session can read it.

## The model you are moving into: a repo that is the company

A Kortix project is a git repo, and the repo is the company. The layout is deliberate:

- `agents/<name>.md` holds what an agent does, in plain markdown, plus an `agents.<name>` block in `kortix.yaml` for its governance and tool reach.
- `skills/` holds reusable know-how: markdown plus scripts that encode how your company does one job.
- `memory/` holds what the company has learned so far, as plain files that accumulate.
- `kortix.yaml` declares the machine image each session boots, the connectors agents may use, the triggers that start work, and the names and grants of secrets. Secret values never appear in the repo ([the docs](https://kortix.com/docs), [Kortix on GitHub](https://github.com/kortix-ai/suna)).

Because all of it is text under version control, you can grep the whole setup, diff any change, and roll any part of it back. That is the property AnythingLLM's in-app configuration cannot offer, and it is the reason the move is worth doing when the job grows.

## The migration checklist

Work down the list in order. Most steps are copying files or writing text; the connector steps are configuration in the dashboard.

1. **Choose where Kortix runs.** Managed cloud, free self-hosting, your VPC, or on-prem. The install steps are in [docs/setup.md](docs/setup.md); the hosting comparison is in the [self-hosting guide](https://anythingllm-alternative.com/self-hosting-anythingllm-alternative.html).
2. **Scaffold the repo.** Run `kortix init`, which creates `kortix.yaml` plus your agents, skills and runtime config ([Kortix on GitHub](https://github.com/kortix-ai/suna)).
3. **Add your model providers.** Any provider with your own keys, your own OpenAI-compatible endpoint, or your ChatGPT subscription, connected in the model picker.
4. **Move your documents.** Copy the source files out of your AnythingLLM workspaces into the repo. Put the distilled knowledge in `memory/` so it accumulates for every future session.
5. **Wire the connectors.** Connect Slack, docs, tickets, CRM and code once. Kortix reaches 3,000+ apps in a click, plus MCP, OpenAPI, Postman, GraphQL and raw HTTP ([kortix.com](https://kortix.com)).
6. **Scope each agent's reach.** Set every tool to Allow, Ask or Block, down to the arguments of each call. An Ask holds the call until a person approves it, then the agent resumes ([Kortix on GitHub](https://github.com/kortix-ai/suna)). AnythingLLM's permissioning decides who can use the instance; this decides what each agent may do, tool by tool.
7. **Set permissions and credentials.** Per-resource permissions cover people and agents, with roles, groups and an audit trail. Connector credentials are brokered server-side and never enter the machine, and approval gates stay off until you set them.
8. **Declare the session image.** Set the machine image in `kortix.yaml`. AnythingLLM runs as one deployment you size once; Kortix boots a fresh cloud computer for every session from the image you name, so a heavy job never slows a light one.
9. **Run the first session and review the change.** Start small: one real task. The agent works on its own branch, opens a change request, and you read the diff before it merges to `main`. Merge is default-deny for agents ([the docs](https://kortix.com/docs)).

## What changes for the team

**Work lands as reviewable changes.** A session produces a diff a person reads and approves, and edits to agents, skills or memory land the same way. Replies in a chat app have no equivalent of that review step.

**Credentials move behind a boundary.** Connector credentials are brokered server-side and never enter the machine a session runs on, and secrets are encrypted at rest with a key per project ([kortix.com](https://kortix.com)).

**Sessions scale sideways.** Thousands of agents in parallel on one config, each on its own cloud computer. What one session learns is available to the next through the memory files ([Kortix on GitHub](https://github.com/kortix-ai/suna)).

## When not to move

Stay if AnythingLLM already fits. It is built for one person, or a small team on a shared Docker instance, chatting privately with local documents, and its MIT license means nothing holds you in now or later. Its own docs advise the desktop app unless you need multi-user support, and multi-user features arrive only with the Docker deployment ([installation overview](https://docs.anythingllm.com/installation-docker/overview), checked October 2026). Move when the work outgrows one app: several people, agents acting across real tools, permissions per resource, and output that lands as reviewed changes. For the decision dimension by dimension, see [docs/vs-anythingllm.md](docs/vs-anythingllm.md).

If the move is yours to make, [Try Kortix](https://kortix.com) on the managed cloud first and self-host when you are ready. The source is [Kortix on GitHub](https://github.com/kortix-ai/suna), so you can read every part of the system before you trust it with work.
