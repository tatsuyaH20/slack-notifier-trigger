# Copilot Instructions

このプロジェクトのコード生成・レビュー・提案を行う際は、下記の規約ファイルを参照してください。

## 規約ファイル一覧

| 対象ファイル | 規約ファイル | 概要 |
| --- | --- | --- |
| `**/*.{js,jsx,ts,tsx}` | [coding-conventions.instructions.md](./instructions/coding-conventions.instructions.md) | 命名規則・関数定義・コメント等のプロジェクト共通規約 |
| `**/*.{ts,tsx}` | [typescript.instructions.md](./instructions/typescript.instructions.md) | 型定義・ユーティリティ型・型安全性に関する TypeScript 固有規約 |
| `**/*.test.tsx` | [test.instructions.md](./instructions/test.instructions.md) | Jest / React Testing Library を使ったテスト記述規約 |

## 優先順位

1. 各ファイルに適用される専用規約（上表参照）
2. `coding-conventions.instructions.md`（全 JS/TS ファイル共通）
