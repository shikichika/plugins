# TypeScript パターン

`SKILL.md` の各ルールに対するコード例である。背後にある原則は言語非依存である。**type-system-discipline** および **boundary-discipline** 原則スキルを参照。

## ブランド型

プリミティブをブランドして混同を防ぐ。境界で一度だけ検証する。下流のコードは型を信頼する。

```ts
type AgentId = string & { readonly __brand: "AgentId" };

function isAgentId(input: string): input is AgentId {
  return isUUID(input);
}

function parseAgentId(input: string): AgentId {
  if (!isAgentId(input)) throw new Error(`Invalid agent id: ${input}`);
  return input;
}

function focusAgent(id: AgentId): void {
  /* input is trusted */
}
```

`readonly __brand: 'X'` の形状に合わせる。新しい規約を作らない。

## 判別可能ユニオン

リテラル判別子でバリアントをモデル化する。各バリアントはフィールド名を共有し、各バリアントの値はユニークなので、矛盾した組み合わせは表現できない。

```ts
// Don't. Boolean + optionals lets contradictory states exist.
type DiffState = { loading: boolean; diff?: GitDiff; error?: string };

// Do. Only valid states exist.
type DiffState =
  | { kind: "loading" }
  | { kind: "ready"; diff: GitDiff }
  | { kind: "error"; error: string };
```

判別子の名前（`kind`、`type`、`tag`）を 1 つ選び、それを使い続ける。

## 構成的モデリング

緩い型を実行時チェックで制限する代わりに、すべて合法な部品から型を組み立てる。

非空、可変長タプルで表現:

```ts
type NonEmpty<T> = [T, ...T[]];

// Don't: T[] plus a length check every caller must repeat
function pickWinner(entries: string[]): string {
  if (entries.length === 0) throw new Error("no entries");
  return entries[Math.floor(Math.random() * entries.length)];
}

// Do: an empty value of the type can't exist
function pickWinner(entries: NonEmpty<string>): string {
  return entries[Math.floor(Math.random() * entries.length)];
}
```

素の `T[]` が渡ってくる場合、ガードで一度絞り込む。その事実は型の中で運ばれる。

```ts
const isNonEmpty = <T>(arr: T[]): arr is NonEmpty<T> => arr.length > 0;
```

偶数長、ペアとして:

```ts
type Pairs<T> = [T, T][];
```

時間範囲、開始と継続時間として:

```ts
// Don't: a comment holds the invariant
type TimeRange = { start: Date; end: Date }; // start <= end

// Do: a negative range can't be written; derive end when needed
type TimeRange = { start: Date; durationMs: number };
```

`durationMs` は素の数値のまま保つ。期間が期待される場所に素の数値が渡されうる場合にのみ（ブランド型に従って）ブランドする。反射的にブランドしてはいけない。悪い状態を構築不能にする表現を選び、必要になったときに読みを上に乗せる（`pairs.flat()`、`rangeEnd()` ヘルパーなど）。

## 最も単純な全域型

すべてを強化しない。`T[]` 上のすべての操作が全域である限り、`T[]` のままにする。

```ts
const sum = (xs: number[]) => xs.reduce((a, b) => a + b, 0); // [] is 0, fine
```

緩い型が使用箇所で嘘を強いるときに強化する。その兆候は `!`、`arr[0] as T`、「起こらないはず」の throw である。

```ts
// Don't: partiality smuggled past the compiler
function newestSession(sessions: Session[]): Session {
  return sessions.at(0)!;
}

// Do: strengthen the input; the assertion disappears
function newestSession(sessions: NonEmpty<Session>): Session {
  return sessions[0];
}
```

戻り値を `Session | undefined` に弱めるのも、もう 1 つの全域シグネチャである。

## `unknown` を `any` より優先

外部データは常に `unknown`。使用前に絞り込む。

```ts
// Don't
function handle(input: any) {
  return input.foo.bar;
}

// Do
function handle(input: unknown) {
  if (typeof input === "object" && input !== null && "foo" in input) {
    // narrowed; compiler verifies access
  }
}
```

外部ソースには RPC ペイロード、`JSON.parse`、`postMessage`、IPC、ファイル内容、環境変数、データベース結果が含まれる。

## 手書きガードの前にスキーマ

外部データに対してプロパティ単位の型ガードを書く前に、リポジトリの実行時スキーマライブラリと既存のスキーマを探す。1 つのスキーマに検証を所有させ、TypeScript の型をそこから導出する。スキーマと、重複したインターフェースと、乖離しうるガードを並存させない。

```ts
import { z } from "zod";

const UserSchema = z.object({
  id: z.string().uuid(),
  role: z.enum(["admin", "member"]),
});

type User = z.infer<typeof UserSchema>;

function parseUser(input: unknown): User {
  return UserSchema.parse(input);
}
```

失敗が想定された分岐である場合は `safeParse` を使う。リポジトリが別のスキーマライブラリを使っているなら、同等の推論ヘルパーを使う。1 つのガードのために新しいスキーマ依存を追加しない。このルールは、コードベースが既に信頼しているスキーマシステムを優先する。

## `as` キャストの禁止

すべての `as` は実行時クラッシュの可能性である。型システムが主張を検証した後にのみキャストする。

```ts
import { z } from "zod";

// Don't
const user = data as User;

// Don't
function isUser(data: unknown): data is User {
  return typeof data === "object" && data !== null && "id" in data;
}

// Do
const userSchema = z.object({ id: z.string(), name: z.string() });
type User = z.infer<typeof userSchema>;

function parseUser(data: unknown): User {
  return userSchema.parse(data);
}
```

型が先にある場合、バリデータにその型を注釈する。コンパイラは型より証明の少ないバリデータを拒否する。下のオブジェクトから `name` を削除すると、代入はコンパイルに失敗する。

```ts
type User = { id: string; name: string };

const userSchema: z.ZodType<User> = z.object({ id: z.string(), name: z.string() });
```

既存コードから `as` をリファクタリングで外すときは、TypeScript が推論できない理由を特定する。

- 判別子の欠如: 判別子を追加し、判別可能ユニオンに切り替える。
- 広すぎる元の型（例: `Record<string, unknown>`）: 絞り込む。
- 未型付けの境界: その形状を所有するスキーマでパースする。スキーマが存在しない場合にのみ追加する。
- 本当に表現不能: ブランド型または `satisfies` を使う。

## 絞り込みの階層

最良から最後の手段まで:

1. **判別可能ユニオンの switch / if。** コンパイラが自動的に絞り込む。
2. **`in` 演算子。** `"key" in obj` はそのキーを含むバリアントに絞り込む。
3. **`typeof` / `instanceof`。** プリミティブとクラスインスタンス用。
4. **ユーザー定義型ガード。** 上記で足りないときに使う。
5. **`as` キャスト。** 検証後にのみ使う。

```ts
function area(s: Shape): number {
  if ("radius" in s) return Math.PI * s.radius ** 2; // narrowed to circle
  return s.width * s.height; // narrowed to rect
}
```

## 型ガード

ガードは主張を実際に検証しなければならない。嘘をつくガードは `as` より悪い。

```ts
function isCircle(s: Shape): s is Shape & { kind: "circle" } {
  return s.kind === "circle";
}
```

可能なら判別子による絞り込みを優先する。

## 網羅性

default アームでは、判別子を `never` 型のローカル変数に代入する。

```ts
// Value-returning switch
function area(s: Shape): number {
  switch (s.kind) {
    case "circle":
      return Math.PI * s.radius ** 2;
    case "rect":
      return s.width * s.height;
    default: {
      const _exhaustive: never = s;
      return _exhaustive;
    }
  }
}

// Void switch
function handle(s: Shape): void {
  switch (s.kind) {
    case "circle":
      drawCircle(s);
      break;
    case "rect":
      drawRect(s);
      break;
    default: {
      const _exhaustive: never = s;
      void _exhaustive;
    }
  }
}
```

値を返す switch では return スタイル、文の switch では void スタイル。

## `satisfies` を `as` より優先

`satisfies` はリテラル型を広げずに検証する。

```ts
// Don't. Widens, loses literal types.
const config = { theme: "dark", cols: 3 } as Config;

// Do. Validates AND preserves literal types.
const config = { theme: "dark", cols: 3 } satisfies Config;
// config.theme is "dark" (literal), not string
```

## 境界での検証

データが入ってくる境界で一度だけ検証する。内部では型を信頼する。**boundary-discipline** 原則スキルを参照。

- **ワイヤフォーマット**（proto、JSON-RPC）: 前方互換な変更が古いクライアントを壊さないように、`ignoreUnknownFields` でパースする。
- **永続化された JSON:** バージョン付きのブロブで、パース周辺を try/catch する。
- **呼び出しチェーンの深部で再検証しない。**

## スキーマ由来の型

`.proto`、OpenAPI 仕様、GraphQL スキーマ、データベースマイグレーションが既に形状を定義しているときは、生成された型から導出し、重複を避ける。

```ts
// Don't. Duplicate shape, drifts when the schema changes.
type CheckSummary = {
  totalCount: number;
  checks: { name: string; status: string }[];
};
function renderChecks(s: CheckSummary) {
  /* ... */
}

// Do. Derive from the generated schema type.
import type { ChecksMessage } from "<generated module>";
function renderChecks(s: Pick<ChecksMessage, "totalCount" | "checks">) {
  /* ... */
}
```

新しいインターフェースを書く前に、`Pick`、`Omit`、`Parameters`、`ReturnType`、`Awaited`、`typeof` に手を伸ばす。

## オブジェクト引数

```ts
// Don't. Swap two args, still compiles.
openFile(uri, {
  startLineNumber: 10,
  startColumn: 1,
  endLineNumber: 10,
  endColumn: 1,
});

// Do. Order-independent, self-documenting.
openFile({
  uri,
  selection: {
    startLineNumber: 10,
    startColumn: 1,
    endLineNumber: 10,
    endColumn: 1,
  },
});
```

ホットパスではスキップする。毎フレームレンダリング、トークナイザ、パーサ、割り当てコストが重要なタイトループ内のものなど。
