# GG Framework

<p align="center">
  <strong>Modular TypeScript framework for building LLM-powered apps. From raw streaming to full coding agent.</strong>
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/@kenkaiiii/ggcoder"><img src="https://img.shields.io/npm/v/@kenkaiiii/ggcoder?style=for-the-badge" alt="npm version"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge" alt="MIT License"></a>
  <a href="https://youtube.com/@kenkaidoesai"><img src="https://img.shields.io/badge/YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white" alt="YouTube"></a>
  <a href="https://skool.com/kenkai"><img src="https://img.shields.io/badge/Skool-Community-7C3AED?style=for-the-badge" alt="Skool"></a>
</p>

Three packages. Each one works on its own. Stack them together and you get a full coding agent.

| Package | What it does | README |
|---|---|---|
| [`@kenkaiiii/gg-ai`](https://www.npmjs.com/package/@kenkaiiii/gg-ai) | Unified LLM streaming API across four providers | [packages/gg-ai](packages/gg-ai/README.md) |
| [`@kenkaiiii/gg-agent`](https://www.npmjs.com/package/@kenkaiiii/gg-agent) | Agent loop with multi-turn tool execution | [packages/gg-agent](packages/gg-agent/README.md) |
| [`@kenkaiiii/ggcoder`](https://www.npmjs.com/package/@kenkaiiii/ggcoder) | CLI coding agent with OAuth, tools, and TUI | [packages/ggcoder](packages/ggcoder/README.md) |

```
@kenkaiiii/gg-ai (standalone)
  └─► @kenkaiiii/gg-agent (depends on gg-ai)
        └─► @kenkaiiii/ggcoder (depends on both)
```

---

## Getting started

```bash
npm i -g @kenkaiiii/ggcoder
ggcoder login
ggcoder
```

Each package can also be installed independently. See the individual READMEs for details.

---

## For developers

```bash
git clone https://github.com/KenKaiii/gg-framework.git
cd gg-framework
pnpm install
pnpm build
```

TypeScript 5.9 + pnpm workspaces + Ink 6 + React 19 + Vitest 4 + Zod v4

---

## Community

- [YouTube @kenkaidoesai](https://youtube.com/@kenkaidoesai) - tutorials and demos
- [Skool community](https://skool.com/kenkai) - come hang out

---

## License

MIT

---

<p align="center">
  <strong>Less bloat. More coding. Four providers. Three packages. One framework.</strong>
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/@kenkaiiii/ggcoder"><img src="https://img.shields.io/badge/Install-npm%20i%20--g%20%40kenkaiiii%2Fggcoder-blue?style=for-the-badge" alt="Install"></a>
</p>
