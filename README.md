# Gene Inspector Pro Agent Skills

Provider-neutral Agent Skills for using [Gene Inspector Pro](https://gene-inspector.pro/) through its read-only remote MCP server.

## Install

Install every skill in this repository:

```bash
npx skills add GeneticPath/gene-inspector-skills
```

Or install only the Gene Inspector Pro skill:

```bash
npx skills add GeneticPath/gene-inspector-skills --skill gene-inspector-pro
```

The installer supports Codex, Claude Code, Cursor, and other agents that read the open `SKILL.md` format.

## Connect Gene Inspector Pro

The skill teaches an agent how to use the Gene Inspector Pro tools, but the agent still needs access to the remote MCP server:

```text
https://mcp.gene-inspector.pro/mcp
```

Follow the [MCP setup guide](https://gene-inspector.pro/mcp) for your client. Sign-in and consent happen on Gene Inspector Pro; do not give your Gene Inspector Pro password to an agent.

## Included skill

- [`gene-inspector-pro`](skills/gene-inspector-pro/SKILL.md): inspect a connected genome, choose a genome safely, explore gene panels and variants, follow pagination, and explain findings within Gene Inspector Pro's educational and non-clinical boundary.

The MCP server reads only the data authorized by the connected Gene Inspector Pro account. The skill does not add write access, upload DNA, diagnose conditions, or recommend treatment.

## License

Apache-2.0. See [LICENSE](LICENSE).
