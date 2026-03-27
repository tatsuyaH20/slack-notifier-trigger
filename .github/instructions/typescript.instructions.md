---
applyTo: "**/*.{ts,tsx}"
---

# TypeScript 固有規約

> **基本規約**: [coding-conventions.instructions.md](./coding-conventions.instructions.md) を必ず参照してください。

## TypeScript の規約

- 型定義は明示的に行うこと
- 型エイリアスには `type` を使用すること（`interface`の使用を避ける）
- ユーティリティ型（`Partial`, `Pick`, `Omit` など）を適切に活用すること
- ジェネリクスは必要な場合にのみ使用すること
- 型推論に頼れる場合は、明示的な型注釈を省略してよい
- 型アサーションの使用は原則避けること（型安全性を損なうため）

## 命名規則

- 型名: PascalCase 機能 + Type (例: `MarginUtilType`)
- その他については [基本規約](./coding-conventions.instructions.md#2-命名規則) を参照すること

## 品質基準

- 型安全性が確保されていること
- コンパイルエラーが発生しないこと
- 不必要な型キャストを避けていること
- 適切なドキュメンテーションコメントを含めていること
