# due-skills

[![skills.sh](https://skills.sh/b/Conansgithub/due-skills)](https://skills.sh/Conansgithub/due-skills)

Due framework agent skills for AI coding agents. This repository packages Due v2 framework references, examples, and development guidance as reusable skills.

Upstream repository: <https://github.com/dobyte/due-skills>

## Install

Use the `skills` CLI:

```bash
npx skills add Conansgithub/due-skills
```

To install directly from the upstream repository instead:

```bash
npx skills add dobyte/due-skills
```

The CLI installs the skills into your local agent environment. After installation, restart or reload your agent if it does not discover new skills automatically.

## Included Skills

### due-skills

Repository-level overview for the Due framework skill collection.

### due-v2

Complete Due v2 framework development reference, including:

- Framework design: architecture, protocols, routing, configuration, runtime modes, error handling, concurrency models, actor model, and rolling updates.
- Core modules: networking, service registration, user locating, RPC transport, cryptography, distributed locks, cache, configuration center, event bus, and logging.
- Cluster services: gate, node, mesh, web server, and client usage.
- Third-party integrations: GORM, Redis, MongoDB, JWT, and Casbin.
- Runnable examples under `skills/due-v2/examples/`.

## Use

After installing, ask your AI agent for Due framework help, for example:

```text
Use the due-v2 skill to create a minimal Due node server.
```

```text
Use the due-v2 skill to explain how gate binding and node binding work.
```

```text
Use the due-v2 skill to choose the right runtime mode and configuration pattern for a production deployment.
```
