# 受 Karpathy 啟發的 GitHub Copilot CLI 指南

> 在 X 上追蹤我：[https://x.com/Will_Huang](https://x.com/Will_Huang)

這是一份單一的 `AGENTS.md` 文件，用來改善 GitHub Copilot CLI 的行為，內容源自 [Andrej Karpathy 的觀察](https://x.com/karpathy/status/2015883857489522876)，整理了 LLM 在程式開發中常見的陷阱。

[English](./README.md) | 正體中文

## 問題所在

來自 Andrej 的推文：

> 「模型會替你做出錯誤假設，然後毫不猶豫地執行。它們不會管理自身的不確定，不會尋求釐清，不會呈現矛盾，不會展示取捨，在應該提出異議時也不會反駁。」

> 「它們真的很喜歡把程式碼和 API 弄得很複雜，堆疊抽象概念，也不清理未使用程式碼……明明 100 行就能完成的事情，偏偏要做成 1000 行的臃腫架構。」

> 「它們有時仍然會改動或刪除自己理解不足的程式碼和註解，即使那些內容與任務本身無關。」

## 解決方案

四個原則，集中在一個文件中，直接對應並解決這些問題：

| 原則 | 解決什麼問題 |
|-----------|-----------|
| 寫程式前先思考 | 錯誤假設、隱藏不確定、缺少取捨分析 |
| 簡潔優先 | 過度複雜、臃腫抽象 |
| 精準修改 | 無關編輯、碰到不該碰的程式碼 |
| 目標驅動執行 | 測試優先、可驗證的成功標準 |

## 四個原則詳解

### 1. 寫程式前先思考

不要假設。不要隱藏不確定。呈現取捨。

LLM 經常會默默選擇一種解讀，然後直接執行。這個原則會強制它把推理說清楚：

- 明確說明假設 — 如果不確定，就詢問，而不是猜測
- 呈現多種解讀 — 當存在歧義時，不要默默替使用者做選擇
- 適時提出異議 — 如果有更簡單的方法，就說出來
- 不確定時停下來 — 指出不清楚的地方，並要求釐清

### 2. 簡潔優先

用最少的程式碼解決問題。不要過度推測需求。

這個原則用來對抗過度工程的傾向：

- 不要加入需求之外的功能
- 不要為一次性程式碼建立抽象
- 不要加入未被要求的「彈性」或「可設定性」
- 不要為不太可能發生的情境加入錯誤處理
- 如果 200 行程式碼可以寫成 50 行，就重寫它

檢驗標準：資深工程師會不會覺得這太複雜？如果會，就簡化。

### 3. 精準修改

只碰必須碰的地方。只清理自己造成的混亂。

編輯既有程式碼時：

- 不要「改善」旁邊的程式碼、註解或格式
- 不要重構沒有壞掉的東西
- 配合既有風格，即使你更偏好另一種寫法
- 如果注意到無關的未使用程式碼，可以提一下，但不要直接刪掉

當你的改動造成孤兒程式碼時：

- 刪除因你的改動而變得無用的 import、變數、函式
- 不要刪除原本就存在的未使用程式碼，除非使用者要求

檢驗標準：每一行修改都應該能直接追溯到使用者的請求。

### 4. 目標驅動執行

定義成功標準。反覆驗證，直到達成。

將指令式任務轉化為可驗證的目標：

| 不要這樣做... | 轉化為... |
|--------------|-----------------|
| 「加入驗證」 | 「為無效輸入撰寫測試，然後讓測試通過」 |
| 「修復 bug」 | 「撰寫能重現 bug 的測試，然後讓測試通過」 |
| 「重構 X」 | 「確保重構前後測試都能通過」 |

對於多步驟任務，說明一個簡短計畫：

```
1. [步驟] → 驗證：[檢查]
2. [步驟] → 驗證：[檢查]
3. [步驟] → 驗證：[檢查]
```

強而有力的成功標準，可以讓 LLM 獨立進行循環式執行。薄弱的標準，例如「讓它可以運作」，則會需要不斷釐清。

## 安裝

選項 A：GitHub Copilot CLI 外掛

先確認你已經安裝 GitHub Copilot CLI。

macOS 或 Linux 使用 Homebrew：

```bash
brew install copilot-cli
```

Windows 使用 WinGet：

```powershell
winget install GitHub.Copilot
```

所有平台也可以使用 npm：

```bash
npm install -g @github/copilot
```

如果 `~/.npmrc` 設定了 `ignore-scripts=true`，請改用：

```bash
npm_config_ignore_scripts=false npm install -g @github/copilot
```

首次使用時，請登入 Copilot CLI：

```bash
copilot login
```

接著加入外掛市集：

```bash
copilot plugin marketplace add doggy8088/andrej-karpathy-skills
```

然後安裝外掛：

```bash
copilot plugin install andrej-karpathy-skills@karpathy-skills
```

這會將指南安裝成 GitHub Copilot CLI 外掛，讓它可以在你的 Copilot CLI 工作流程中使用。

選項 B：AGENTS.md（依專案設定）

新專案：

```bash
curl -o AGENTS.md https://raw.githubusercontent.com/doggy8088/andrej-karpathy-skills/main/AGENTS.md
```

既有專案（追加內容）：

```bash
echo "" >> AGENTS.md
curl https://raw.githubusercontent.com/doggy8088/andrej-karpathy-skills/main/AGENTS.md >> AGENTS.md
```

GitHub Copilot CLI 會自動讀取位於 Git root 或目前工作目錄中的 `AGENTS.md`，因此你可以把這份指南放在 repository 根目錄，作為專案層級的 Copilot CLI 指示。

## 在 Cursor 中使用

本倉庫包含一個已提交的 Cursor 專案規則 ([`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc))，因此在 Cursor 中開啟專案時，這些指南同樣會生效。詳細內容請參考 [CURSOR.md](CURSOR.md)，其中包含如何在其他專案中使用該規則，以及它與 GitHub Copilot CLI 的關係。

## 核心洞察

來自 Andrej：

> 「LLM 非常擅長循環執行，直到達成特定目標……不要告訴它該做什麼，給它成功標準，然後看著它完成。」

「目標驅動執行」原則正是捕捉了這一點：將指令式指令，轉化為帶有驗證循環的宣告式目標。

## 如何判斷它正在發揮作用

如果你看到以下情況，代表這些指南正在發揮作用：

- diff 中不必要的改動更少 —— 只有被請求的改動出現
- 因過度複雜而導致的重寫更少 —— 程式碼第一次就寫得簡潔
- 釐清問題會在實作之前提出 —— 而不是犯錯之後才問
- 乾淨、精簡的 PR —— 沒有順手做的重構或「改善」

## 客製化

這些指南的設計目標，是可以與專案特定指令合併使用。你可以將它們加入既有的 `AGENTS.md`，或建立一份新的。

若要加入專案特定規則，可以新增如下章節：

```markdown
## 專案特定指南

- 使用 TypeScript 嚴格模式
- 所有 API 端點都必須有測試
- 遵循 `src/utils/errors.ts` 中既有的錯誤處理模式
```

## 取捨說明

這些指南偏向謹慎，而不是速度。對於瑣碎任務，例如簡單的錯字修正、顯而易見的一行修改，請自行判斷；並不是每一次改動都需要完整而嚴謹的流程。

目標是減少非瑣碎工作中代價高昂的錯誤，而不是拖慢簡單任務。

## 授權

MIT
