---
applyTo: "**/*.test.tsx"
---

# テスト 固有規約

> **基本規約**: [coding-conventions.instructions.md](./coding-conventions.instructions.md) を必ず参照してください。

## テストの規約

- テストはJestとReact Testing Libraryを使用すること
- テストの基本構造は `describe` と `it` で構成すること
- コンポーネントのレンダリングには `render` 関数を使用すること
- アサーションには Jest の期待値マッチャーを使用すること（例：`expect().toBeInTheDocument()`）
- モックが必要な場合は `jest.mock()` または `jest.fn()` を使用すること
- スタイルのテストは適用したいスタイルの TailwindCSS クラスがあるかどうかで判断すること

## テスト内容

- 以下の項目を必ずテストすること：
  1. コンポーネントが正しくレンダリングされること
  2. プロップが適切に適用されること
  3. イベントハンドラが正しく実行されること
  4. 条件付きレンダリングの検証
- 境界値や特殊なケースも考慮したテストケースを含めること
- スナップショットテストは必要最小限とすること

## テスト命名規則

- テスト名は日本語で記述し、「何をテストするか\_どのような結果が期待されるか」の形式とすること

## 品質基準

- すべてのテストがパスすること
- カバレッジが80%以上であること
- アクセシビリティに関するテストを含めること

## Container コンポーネントのテスト戦略

Container コンポーネントのテストでは、テスト目的に応じて適切な戦略が必要です。

**3つのテスト戦略概要**：

1. **コンポーネントテスト** - 実際のUIコンポーネントを使用してビジネスロジックとUIの統合動作を検証
2. **Containerテスト** - UIをモック化してContainerのロジック処理のみを検証
3. **Mockコンポーネントテスト** - Mock自体にロジックがある場合のみ意味を持つ特殊なテスト

### コンポーネントテスト: UI をモックしない

**目的**: ビジネスロジックと真実のUI コンポーネントの協調動作を検証する
**適用場面**: 機能全体の動作確認、UI部品の検証

```tsx
// ✅ 推奨: hooks/services のみをモック、UI コンポーネントは真実を使用
jest.mock("@/hooks/useUserPermissions/useUserPermissions", () => ({
  useUserPermissions: jest.fn(),
}));
jest.mock("@/services/client/myCatalog/postFavoriteItem.service");

describe("MyCatalogFolderCreationFormContainer_結合テスト", () => {
  it("ユーザー権限の変更が UI 表示に正しく反映される", () => {
    // 1. ビジネスロジックの状態を設定
    useUserPermissions.mockReturnValue({
      sharePermissions: { personal: true, department: false, company: true },
    });

    renderWithStore(<MyCatalogFolderCreationFormContainer {...props} />);

    // 2. 真実の UI コンポーネントでの結果を検証
    expect(screen.getByText("個人")).toBeInTheDocument();
    expect(screen.queryByText("部門共有")).not.toBeInTheDocument();
    expect(screen.getByText("企業共有")).toBeInTheDocument();
  });
});
```

### Containerテスト: UI をモックする

**目的**: Container の業務逻辑処理とデータフローを隔離して検証する
**適用場面**: 複雑な状態管理、データ変換ロジック、条件分岐の検証

```tsx
// ✅ hooks/services と UI コンポーネントの両方をモック
jest.mock("@/hooks/useRecentCheckedItems/useRecentCheckedItems");
jest.mock("@/components/recentChecked/RecentCheckedItemList/RecentCheckedItemList", () => ({
  RecentCheckedItemList: ({ showEmptyMessage, itemList, currentPage }) => (
    <div data-testid="recent-checked-list">
      <div data-testid="empty-message">{showEmptyMessage.toString()}</div>
      <div data-testid="item-count">{itemList.length}</div>
      <div data-testid="current-page">{currentPage}</div>
    </div>
  ),
}));

describe("RecentCheckedItemListContainer_Containerテスト", () => {
  it("空のデータリストに対して正しい状態が設定される", () => {
    // 1. ビジネスロジックの状態を設定
    mockUseRecentCheckedItems.mockReturnValue({
      items: [],
      isInitialized: true,
    });

    render(<RecentCheckedItemListContainer />);

    // 2. Container のロジック処理結果を検証（UI の実装詳細ではなく）
    expect(screen.getByTestId("empty-message")).toHaveTextContent("true");
    expect(screen.getByTestId("item-count")).toHaveTextContent("0");
  });
});
```

### Mock コンポーネントのテスト

Mock コンポーネントのテストは以下の場合のみ意味がある：

**✅ 意味のあるパターン**: ロジックや状態管理を含む Mock

```tsx
// 価値のある Mock 例
export const InteractiveMock = ({ item }) => {
  const [isProcessing, setIsProcessing] = useState(false);

  const handleClick = async () => {
    setIsProcessing(true);
    console.log("Processing item:", item.id);
    await mockApiCall(item);
    setIsProcessing(false);
  };

  return <RealComponent onAction={handleClick} disabled={isProcessing} />;
};

describe("InteractiveMock_有効なテスト", () => {
  it("クリック時に適切な処理フローが実行される", async () => {
    const item = { id: "123" };
    render(<InteractiveMock item={item} />);

    await userEvent.click(screen.getByRole("button"));

    // Mock 自体のロジックをテスト
    expect(mockApiCall).toHaveBeenCalledWith(item);
  });
});
```

**❌ 避けるべきパターン**: 単純なMock自身のテスト

```tsx
// 無意味なテスト例
export const SimpleMock = (props) => <RealComponent {...props} />;

// このようなテストは価値がない
it("コンポーネントが正しくレンダリングされること", () => {
  render(<SimpleMock {...props} />);
  expect(MockedRealComponent).toHaveBeenCalledWith(props);
});
```

### テスト戦略の選択指針

| 検証目的           | モック戦略      | 使用場面       | メンテナンス     |
| ------------------ | --------------- | -------------- | ---------------- |
| **機能完全性**     | 結合テスト      | 機能リリース前 | 高い信頼性       |
| **ロジック正確性** | Containerテスト | 開発中の検証   | 低いメンテナンス |
| **Mock ツール**    | Mock テスト     | 開発ツール検証 | 必要時のみ       |

## モック/ネットワーク

- 実ネットワークアクセス（fetch/axios の生呼び出し）
- 外部通信は MSW（Mock Service Worker）でスタブすることを推奨
- 例外系（404/500など）を含むシナリオを網羅する

## テスト配置と命名

- テストは対象ファイルと同じディレクトリに `*.test.tsx` として配置する
- describe/it は日本語で簡潔に目的を示す
- 長いシナリオは `describe` を分けて責務を明確にする

## パフォーマンス/安定性

- DOM 実装に依存した脆弱なセレクタ（id や複雑な CSS セレクタ）
- タイマーは `jest.useFakeTimers()` を活用し、`setTimeout` 依存を排除する
- 不要な `console.*` は `jest.spyOn(console, 'error')` などで監視し発生しないことを検証する
