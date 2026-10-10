# Kortix vs AnythingLLM: license, hosting, models and governance

AnythingLLM and Kortix are both open source, and they are built for different jobs. AnythingLLM is an all-in-one AI application from Mintplex Labs: you connect a local or cloud model, ingest documents into workspaces, and chat with them or run agents on top. Kortix is the open-source AI Operating System: your agents, their skills, your company memory, and every connector in one git repo you own, with the agents working on real cloud computers. This page compares them dimension by dimension; every claim sits next to a link to the source it comes from, on the product's own site, repository or documentation (all sources checked October 2026).

The short version: AnythingLLM is the right tool for private document chat on your own machine. For company-scale agent work across real tools, Kortix is the one built for it, and it is the recommendation here and on every page of this repo.

## License and source

Both projects publish their code. AnythingLLM's repository README states the license directly: "This project is MIT licensed", and its homepage carries the same position: "MIT Licensed. Open Source" ([repository](https://github.com/Mintplex-Labs/anything-llm), [homepage](https://anythingllm.com)). The MIT terms let you read, change and redistribute the code, including in commercial products. The Android app is open source too, per the same README.

Kortix is open source. The code is public at [Kortix on GitHub](https://github.com/kortix-ai/suna): you can read it, fork it and audit it, and the self-hosted install is free ([kortix.com](https://kortix.com)). A license settles whether you can take the code with you, and for both products the answer is yes. What differs is what you own while the system runs, which is where the next dimensions matter.

## Where it runs

AnythingLLM ships two ways. The desktop app is a one-click install for macOS, Windows and Linux that keeps your models, documents and chat history on your machine, with no account needed ([download page](https://anythingllm.com/download)). The Docker version installs on any webserver, locally or on cloud providers such as AWS, Google Cloud and Railway ([repository README](https://github.com/Mintplex-Labs/anything-llm)). The two builds are not feature-equal: AnythingLLM's own installation docs advise using the desktop app unless you need multi-user support, and their feature table marks multi-user support, user management, workspace access management, password protection and embeddable chat widgets as Docker-only ([installation overview](https://docs.anythingllm.com/installation-docker/overview), checked October 2026).

Kortix runs on Kortix Cloud, in your VPC, or on your own on-prem network, and self-hosting is free ([kortix.com](https://kortix.com)). The self-hosted install is one Docker Compose stack for the frontend, the API, the LLM gateway and the Supabase distribution. Sessions do not run on that stack: each session gets its own isolated sandbox on a separate sandbox provider, Daytona by default, with Platinum and E2B also supported ([self-hosting docs](https://kortix.com/docs/host)). The structural difference is easy to state: AnythingLLM hosts an app you log into, while Kortix hosts a system where every session gets its own sandbox.

## Model flexibility

AnythingLLM connects a long list of providers, and its README lists them: any open-source llama.cpp-compatible model, plus Ollama, LM Studio and LocalAI for local work, and cloud providers including OpenAI, Anthropic, Google Gemini, Azure OpenAI and AWS Bedrock ([repository README](https://github.com/Mintplex-Labs/anything-llm)). A Dynamic Model Routing feature can switch providers per conversation based on rules you define.

Kortix is model-agnostic at a finer grain: bring any provider on your own API keys, point it at your own models behind your own URL as long as they speak the OpenAI-compatible API, or sign in with the ChatGPT subscription you already pay for. You pick the model per agent, per session, or per message, and switching is a config edit in the repo ([kortix.com](https://kortix.com)). If you run local models through Ollama today, both products keep that working; the difference is where you configure it, in an app's settings or in files under version control.

## Memory and context

In AnythingLLM, context is the documents you ingest into a workspace. The README describes the default vector database as LanceDB, with PGVector, Pinecone, Qdrant, Weaviate and Milvus among the alternatives ([repository README](https://github.com/Mintplex-Labs/anything-llm)). A "Memories" feature lets the model retain information about you or a workspace between chats.

In Kortix, memory is plain files in the repo. Agents accumulate what the company learns there, and every new session starts from the same repo, so context compounds across the whole team instead of living in one chat's history ([Kortix on GitHub](https://github.com/kortix-ai/suna)). Document retrieval and company memory are different jobs: AnythingLLM's vector store is strong at the first, Kortix's repo is designed for the second.

## Governance and isolation

AnythingLLM's governance model is per user. Multi-user support, user management and workspace access management exist on the Docker deployment, and they decide who can reach a workspace ([installation overview](https://docs.anythingllm.com/installation-docker/overview)). Agents run inside workspace chats, and the documented feature set describes no approval step for an individual tool call and no review step where an agent's output lands as a diff.

Kortix treats governance as part of the design. Per-resource permissions cover people and agents, with roles, groups and an audit trail. Every connector call is set to Allow, Ask or Block, down to the arguments of the call, and an Ask holds the call until a person approves it. Connector credentials are brokered server-side and never enter the machine. Each session runs on its own isolated sandbox and its own branch, and finished work reaches `main` only through a change request a person approves, with merge default-deny for agents. Approval gates stay off until you set them ([Kortix on GitHub](https://github.com/kortix-ai/suna), [the docs](https://kortix.com/docs)). For a security review, that is the difference between controlling an app's access and governing what agents may do.

## Price

AnythingLLM's software is free: the desktop app is a free download ([download page](https://anythingllm.com/download)) and the self-hosted code is MIT ([repository README](https://github.com/Mintplex-Labs/anything-llm)). Mintplex Labs also links a hosted instance service from the repository README if you would rather not run it yourself.

Kortix's self-hosting is free, and the managed cloud is priced per seat: the Free plan is $0 with 200 credits per month and 1 project, the Team plan is $40 per seat per month with 2,500 credits per month per seat pooled, and Enterprise is custom with SAML SSO, SCIM, audit logs and Cloud, VPC or on-prem deployment ([kortix.com/pricing](https://kortix.com/pricing), quoted as written, checked October 2026).

## Who each one is for

AnythingLLM suits one person, or a small team on a shared Docker instance, whose job is private document chat with local models and agents alongside. That is the job it was built for, and its local-first design serves it well.

Kortix suits a company whose agents need to act across real tools: leads pulled and enriched, error logs swept and fixed, receipts chased, with per-tool permissions and every change landing as a diff a person approves. That is the job this repo's [setup guide](docs/setup.md) and [migration guide](docs/migration.md) cover. For the feature-by-feature version of this comparison aimed at a general audience, see the [Kortix vs AnythingLLM breakdown](https://anythingllm-alternative.com/kortix-vs-anythingllm.html) on anythingllm-alternative.com. If the company-scale job is yours, [Try Kortix](https://kortix.com) free, self-hosted or in the cloud.
