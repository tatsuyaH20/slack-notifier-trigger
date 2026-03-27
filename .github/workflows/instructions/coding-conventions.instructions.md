---
applyTo: "**/*.{js,jsx,ts,tsx}"
---

# 統合コーディング規約

このファイルは、プロジェクト全体の統一されたコーディング規約を定義します。
**すべてのコード作成・レビュー時にはこのファイルを参照してください。**

# コーディング規則

## 1. 基本方針

### 1.1 可読性重視

- コードは書く時間より読む時間の方が長いことを意識する
- 明確で分かりやすい命名を心がける
- 適切なコメントを記述する

### 1.2 一貫性の維持

- プロジェクト内で統一されたスタイルを使用する
- チーム内でのコーディング規約を遵守する

### 1.3 保守性の確保

- 変更しやすい構造にする
- 依存関係を最小限に抑える
- テストしやすいコードにする

## 2. 命名規則

| 種類                   | 命名規則               | 例        |
| ---------------------- | ---------------------- | --------- |
| コンポーネント名       | アッパーキャメルケース | UserForm  |
| 変数名                 | ローワーキャメルケース | userName  |
| プリミティブ型の定数名 | スネークケース         | API_URL   |
| オブジェクト型の定数名 | ローワーキャメルケース | userName  |
| メソッド名             | ローワーキャメルケース | addNumber |
| プロパティ名           | ローワーキャメルケース | userName  |
| クラス名               | アッパーキャメルケース | MyCar     |

### 2.1 具体例

#### 変数・関数名（ローワーキャメルケース）

```typescript
const userName = "John";
const calculateTotalPrice = () => {};
const sampleFunction = () => {};
```

#### 定数（スネークケース）

```typescript
const MAX_RETRY_COUNT = 3;
const API_BASE_URL = "https://api.example.com";
const API_URL = "https://api.example.com/v1";
```

#### クラス名・コンポーネント名（アッパーキャメルケース）

```typescript
class UserService {}
class MyCar {}
const UserForm = () => {};
```

#### プロパティ名（ローワーキャメルケース）

```typescript
type User = {
  userName: string;
  emailAddress: string;
  lastLoginDate: Date;
};
```

## 3. 関数とアロー関数

### 3.1 アロー関数で統一

- 全ての関数はアロー関数で記述する
- function宣言は使用しない

```typescript
// Good
const calculateTotalPrice = (price: number, tax: number): number => {
  return price * (1 + tax);
};

const validateEmail = (email: string): boolean => {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};

// Bad
function calculateTotalPrice(price: number, tax: number): number {
  return price * (1 + tax);
}
```

### 3.2 エクスポートルール

- 原則として1ファイルにつき1つの関数をエクスポートする
- 名前付きエクスポートを使用する
- 可読性向上のため、関連する複数の関数を同一ファイルでエクスポートすることも可能

```typescript
// userService.ts（単一エクスポートの場合）
export const getUserById = async (id: string): Promise<User> => {
  // 実装
};
```

```typescript
// userValidator.ts（複数エクスポートの場合）
export const validateUserData = (userData: UserData): ValidationResult => {
  // 実装
};

export const validateUserEmail = (email: string): boolean => {
  // 実装
};
```

## 4. Enum の実装

### 4.1 Object as const で実装

- enumキーワードは使用しない
- Object as constパターンを使用する

```typescript
// Good
const userRole = {
  admin: "admin",
  user: "user",
  guest: "guest",
} as const;

type UserRole = (typeof userRole)[keyof typeof userRole];

const userStatus = {
  active: 1,
  inactive: 0,
  pending: 2,
} as const;

type UserStatus = (typeof userStatus)[keyof typeof userStatus];

// Bad
enum UserRole {
  ADMIN = "admin",
  USER = "user",
  GUEST = "guest",
}
```

### 4.2 使用例

```typescript
const checkUserPermission = (role: UserRole): boolean => {
  return role === userRole.admin;
};

const updateUserStatus = (status: UserStatus): void => {
  if (status === userStatus.active) {
    // アクティブユーザーの処理
  }
};
```

## 5. コード構造

### 5.1 関数・メソッドの設計原則

- 単一責任の原則に従う
- 1つの関数は1つのことだけを行う
- 引数は必要最小限に抑える（理想は2個以下）
- 引数が3個以上の場合は、必ず一つの連想配列（オブジェクト）にまとめる
- 可能な限り純粋関数にする（副作用を避ける）

```typescript
// Good - 純粋関数（副作用なし）
const validateEmail = (email: string): boolean => {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};

const addNumbers = (a: number, b: number): number => {
  return a + b;
};

const calculateTax = (price: number, taxRate: number): number => {
  return price * taxRate;
};

// Good - 引数が3個以上の場合は連想配列にまとめる
type CreateUserParams = {
  name: string;
  email: string;
  age: number;
  address: string;
  phone: string;
  role: string;
};

const createUser = (params: CreateUserParams): User => {
  const { name, email, age, address, phone, role } = params;
  return {
    id: generateId(),
    name: name.trim(),
    email: email.toLowerCase(),
    age,
    address,
    phone,
    role,
    createdAt: new Date(),
  };
};

// Good - 副作用がある場合は明確に分離
const saveUserToDatabase = async (user: User): Promise<void> => {
  // データベース操作（副作用）
  await database.save(user);
};

const logUserAction = (action: string, userId: string): void => {
  // ログ出力（副作用）
  console.log(`User ${userId} performed ${action}`);
};

// Bad - 引数が多すぎる（3個以上の場合は連想配列にまとめるべき）
const processUserBad = (
  name: string,
  email: string,
  age: number,
  address: string,
  phone: string,
  role: string,
): void => {
  // 実装
};

// Bad - 副作用が混在
const processUser = (name: string, email: string, age: number, address: string, phone: string, role: string): void => {
  // 計算処理
  const user = {
    id: generateId(),
    name: userData.name.trim(),
    email: userData.email.toLowerCase(),
    createdAt: new Date(),
  };

  // 副作用が混在
  await database.save(user); // データベース操作
  logger.info("User saved"); // ログ出力

  return user;
};

const calculatePriceAndLog = (price: number, taxRate: number): number => {
  const result = price * (1 + taxRate);
  console.log(`Calculated price: ${result}`); // 副作用が混在
  return result;
};
```

### 5.2 クラスの設計

- 単一責任の原則
- 適切なカプセル化
- 継承より合成を優先

```typescript
class MyCar {
  private brand: string;
  private model: string;

  constructor(brand: string, model: string) {
    this.brand = brand;
    this.model = model;
  }

  getBrand = (): string => {
    return this.brand;
  };

  getModel = (): string => {
    return this.model;
  };
}
```

### 5.3 ファイル構成

```
src/
├── components/     # UIコンポーネント
├── services/       # ビジネスロジック
├── utils/          # ユーティリティ関数
└── constants/      # 定数
```

#### 5.3.1 モジュール単位のディレクトリ構成

各モジュールは同名のディレクトリ内に配置し、テストファイルも同じディレクトリに含める。

```
src/
├── services/
│   ├── userService/
│   │   ├── userService.ts
│   │   └── userService.test.ts
│   └── paymentService/
│       ├── paymentService.ts
│       └── paymentService.test.ts
├── utils/
│   ├── validateEmail/
│   │   ├── validateEmail.ts
│   │   └── validateEmail.test.ts
│   └── formatDate/
│       ├── formatDate.ts
│       └── formatDate.test.ts
└── components/
    ├── UserCard/
    │   ├── UserCard.tsx
    │   └── UserCard.test.tsx
    └── PaymentForm/
        ├── PaymentForm.tsx
        └── PaymentForm.test.tsx
```

### 5.4 副作用の管理

#### 5.4.1 純粋関数の推奨

- 同じ入力に対して常に同じ出力を返す
- 外部の状態を変更しない
- 外部の状態に依存しない

```typescript
// Good - 純粋関数
const formatCurrency = (amount: number, currency: string): string => {
  return `${amount.toFixed(2)} ${currency}`;
};

const isValidAge = (age: number): boolean => {
  return age >= 0 && age <= 150;
};

// Bad - 外部状態に依存
let globalCounter = 0;
const incrementCounter = (): number => {
  return ++globalCounter; // グローバル変数を変更
};
```

#### 5.4.2 副作用がある処理の分離

- I/O操作（API呼び出し、ファイル操作）
- データベース操作
- ログ出力
- 状態変更

```typescript
// Good - 純粋な計算部分と副作用を分離
const calculateOrderTotal = (items: OrderItem[]): number => {
  return items.reduce((total, item) => total + item.price * item.quantity, 0);
};

const saveOrder = async (order: Order): Promise<void> => {
  await orderRepository.save(order);
};

const processOrder = async (items: OrderItem[]): Promise<void> => {
  const total = calculateOrderTotal(items); // 純粋関数
  const order = { items, total, createdAt: new Date() };
  await saveOrder(order); // 副作用
};

// Bad - 計算と副作用が混在
const processOrderBad = async (items: OrderItem[]): Promise<number> => {
  let total = 0;
  for (const item of items) {
    total += item.price * item.quantity;
    console.log(`Processing item: ${item.name}`); // 副作用が混在
  }
  await orderRepository.save({ items, total }); // 副作用が混在
  return total;
};
```

#### 5.4.3 関数型プログラミングの活用

- map、filter、reduceなどの純粋な配列メソッドを活用
- イミュータブルな操作を心がける

```typescript
// Good - 関数型アプローチ
const getActiveUsers = (users: User[]): User[] => {
  return users.filter((user) => user.status === "active");
};

const getUserNames = (users: User[]): string[] => {
  return users.map((user) => user.name);
};

const calculateAverageAge = (users: User[]): number => {
  const totalAge = users.reduce((sum, user) => sum + user.age, 0);
  return totalAge / users.length;
};

// Bad - 元の配列を変更
const sortUsersBad = (users: User[]): User[] => {
  return users.sort((a, b) => a.name.localeCompare(b.name)); // 元配列を変更
};

// Good - 元の配列を変更しない
const sortUsers = (users: User[]): User[] => {
  return [...users].sort((a, b) => a.name.localeCompare(b.name));
};
```

## 6. コメント規則

### 6.1 JSDoc形式

```typescript
/**
 * ユーザー情報を取得する
 * @param userId - ユーザーID
 * @param options - オプション設定
 * @returns ユーザー情報のPromise
 * @throws ユーザーが見つからない場合
 */
const getUser = async (userId: string, options: GetUserOptions = {}): Promise<User> => {
  // 実装
};
```

### 6.2 インラインコメント

```typescript
// 複雑なロジックの説明
const adjustedPrice = basePrice * (1 + taxRate) - discount;

// TODO: より効率的なアルゴリズムに変更
// FIXME: エラーハンドリングが不完全
// HACK: 一時的な回避策
```

## 7. エラーハンドリング

### 7.1 例外処理

```typescript
const fetchUserData = async (userId: string): Promise<User> => {
  try {
    const result = await apiCall(userId);
    return result;
  } catch (error) {
    console.error("API呼び出しでエラーが発生しました", { error: (error as Error).message });
    throw new Error("データの取得に失敗しました");
  }
};
```

### 7.2 エラーメッセージ

- ユーザーにとって理解しやすい内容
- 開発者にとってデバッグしやすい情報を含む
- セキュリティ情報を漏洩させない

## 8. テスト

### 8.1 テストファイル配置

- テストファイルはテスト対象のソースと同じディレクトリに配置する
- モジュールと同名の親ディレクトリを持つ構造にする
- ファイル名は `[モジュール名].test.ts` とする

```
// Good - 推奨されるファイル構造
src/
├── services/
│   ├── userService/
│   │   ├── userService.ts
│   │   └── userService.test.ts
│   └── paymentService/
│       ├── paymentService.ts
│       └── paymentService.test.ts
└── utils/
    ├── validateEmail/
    │   ├── validateEmail.ts
    │   └── validateEmail.test.ts
    └── formatDate/
        ├── formatDate.ts
        └── formatDate.test.ts

// Bad - 避けるべき構造
src/
├── services/
│   ├── userService.ts
│   └── paymentService.ts
└── __tests__/
    ├── userService.test.ts
    └── paymentService.test.ts
```

### 8.2 テストの命名

```typescript
// テスト名は何をテストするかを明確に
describe("UserService", () => {
  describe("validateEmail", () => {
    it("有効なメールアドレスの場合trueを返す", () => {
      // テスト実装
    });

    it("無効なメールアドレスの場合falseを返す", () => {
      // テスト実装
    });
  });
});
```

### 8.3 テストケース

- 正常系（Happy Path）
- 異常系（Error Path）
- 境界値テスト
- エッジケース

## 9. パフォーマンス

### 9.1 基本原則

- 早すぎる最適化は避ける
- ボトルネックを特定してから最適化する
- 計測可能な改善を行う

### 9.2 具体的な対策

```typescript
// 不要な再計算を避ける（React例）
const memoizedFunction = useMemo(() => {
  return expensiveCalculation(data);
}, [data]);

// 非同期処理の最適化
const fetchAllUsers = async (urls: string[]): Promise<User[]> => {
  const promises = urls.map((url) => fetch(url));
  const results = await Promise.all(promises);
  return results.map((res) => res.json());
};
```

## 10. セキュリティ

### 10.1 入力値検証

```typescript
const sanitizeInput = (input: unknown): string => {
  if (typeof input !== "string") {
    throw new Error("Invalid input type");
  }
  return input.trim().replace(/[<>]/g, "");
};
```

### 10.2 機密情報の管理

- 環境変数を使用
- ハードコーディングしない
- 適切な権限管理

```typescript
// Good
const API_KEY = process.env.API_KEY;

// Bad
const API_KEY = "sk-1234567890abcdef";
```

## 11. Git管理

### 11.1 コミットメッセージ

コミットメッセージのルールは、専用の指示ファイルで定義されています。
詳細は `.github/instructions/commit-message.instructions.md` を参照してください。

### 11.2 ブランチ戦略

```
main/master    # 本番環境
develop        # 開発環境
feature/*      # 機能開発
hotfix/*       # 緊急修正
release/*      # リリース準備
```

## 12. ドキュメント

### 12.1 README

- プロジェクトの概要
- インストール・セットアップ手順
- 使用方法
- 貢献方法

### 12.2 API仕様書

- エンドポイント一覧
- リクエスト・レスポンス例
- エラーコード
- 認証方法

## 13. チェックリスト

### 13.1 コードレビュー前

- [ ] 命名規則に従っている
- [ ] アロー関数で統一されている
- [ ] 原則1ファイル1エクスポートのルールに従っている
- [ ] 名前付きエクスポートで統一されている
- [ ] enumがObject as constで実装されている
- [ ] 関数の引数が3個以上の場合は連想配列にまとめられている
- [ ] 可能な限り純粋関数で実装されている
- [ ] 副作用がある処理が適切に分離されている
- [ ] 適切なコメントが記述されている
- [ ] テストが書かれている
- [ ] テストファイルが適切なディレクトリ構造で配置されている
- [ ] エラーハンドリングが実装されている
- [ ] セキュリティ要件を満たしている

### 13.2 デプロイ前

- [ ] 全テストが通過している
- [ ] Biomeでエラーがない
- [ ] TypeScriptコンパイルが成功している
- [ ] パフォーマンステストを実施済み
- [ ] セキュリティチェックを実施済み
- [ ] ドキュメントが更新されている
