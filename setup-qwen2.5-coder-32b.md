# Qwen2.5-Coder 32B セットアップガイド

**目的**: Qwen2.5-Coder 32B をローカル環境（Ollama）でセットアップ
**推奨用途**: コーディング特化エージェント（Alfred 候補）

⚠️ **重要**: このガイドは**手順のみ**を記載。**PC再起動後に実施すること**。

---

## システム要件

| 項目 | 最小要件 | 推奨 |
|------|---------|------|
| RAM | 32GB | 64GB |
| ディスク空き容量 | 22GB | 50GB |
| CPU | 8コア | 16コア |

**Mac Studio M2 Max 32GB の場合**:
- ギリギリ動作可能（スワップ発生のリスクあり）
- **他のアプリケーションを終了してから実行**

---

## Qwen2.5-Coder 32B の特徴

| 項目 | 性能 |
|------|------|
| HumanEval スコア | 92.7%（GPT-4o と同等） |
| コンテキスト長 | 32K トークン |
| 用途 | コード生成、レビュー、リファクタリング |
| 推論能力 | ⭐⭐⭐⭐（DeepSeek-R1 より低い） |

---

## 事前準備（必須）

### 1. メモリ解放

**PC再起動**が最も確実。または以下を終了:
- VSCode（3.8 GB 解放）
- Arc Browser のタブ整理（1-2 GB 解放）
- Slack、Discord（0.5 GB 解放）

**目標空きメモリ**: 10 GB 以上

### 2. ディスク空き容量確認

```bash
df -h ~
# 空き容量が 22GB 以上あることを確認
```

---

## セットアップ手順

### Step 1: モデルダウンロード

⚠️ **注意**: ダウンロードには 10-20分かかる

```bash
# Qwen2.5-Coder 32B をダウンロード
ollama pull qwen2.5-coder:32b
```

**ダウンロード中のメモリ使用**:
- ダウンロード自体は 1-2 GB
- 展開時に一時的に 5-10 GB 使用

---

### Step 2: インストール確認

```bash
# インストール済みモデルを確認
ollama list | grep qwen2.5-coder:32b

# 期待される出力:
# qwen2.5-coder:32b    [ID]    19 GB    [日時]
```

---

### Step 3: メモリ監視スクリプトの準備

DeepSeek-R1 32B と同じスクリプトを使用。

```bash
# メモリ監視スクリプトを確認
ls -lh ~/workspace-ai/nomuraya-llm/model-deepseek/monitor-memory.sh

# 存在しない場合は作成（DeepSeek-R1 32B のガイド参照）
```

---

### Step 4: 動作検証（軽量テスト）

⚠️ **重要**: 初回は**軽量プロンプト**でテスト

```bash
# ターミナル1: メモリ監視
~/workspace-ai/nomuraya-llm/model-deepseek/monitor-memory.sh

# ターミナル2: Qwen2.5-Coder 32B を起動
echo "def hello():" | ollama run qwen2.5-coder:32b
```

**判定基準**:
- Free メモリが 2 GB 以上 → **安全、本格利用可能**
- Free メモリが 1-2 GB → **注意、短時間利用のみ**
- Free メモリが 1 GB 未満 → **危険、即座に停止**

---

### Step 5: 停止方法

```bash
# プロセスを停止
pkill -f "ollama.*qwen2.5-coder:32b"

# または Ctrl+C で終了
```

---

## 本格利用（動作検証後）

### テストスクリプト作成

```python
# ~/workspace-ai/nomuraya-llm/model-qwen/test-qwen2.5-coder-32b.py
import subprocess
import json

def test_qwen2_5_coder_32b():
    """Qwen2.5-Coder 32B の動作確認"""

    messages = [
        {
            "role": "system",
            "content": "あなたはコーディング特化の AI アシスタントです。"
        },
        {
            "role": "user",
            "content": "Python で簡単な Hello World 関数を書いてください。"
        }
    ]

    # Ollama API 呼び出し（JSON形式）
    payload = {
        "model": "qwen2.5-coder:32b",
        "messages": messages,
        "stream": False
    }

    # curl で API 呼び出し
    cmd = [
        "curl", "-X", "POST",
        "http://localhost:11434/api/chat",
        "-H", "Content-Type: application/json",
        "-d", json.dumps(payload)
    ]

    result = subprocess.run(cmd, capture_output=True, text=True)
    response = json.loads(result.stdout)

    print("=" * 60)
    print("Qwen2.5-Coder 32B からの応答:")
    print("=" * 60)
    print(response['message']['content'])
    print("=" * 60)

if __name__ == "__main__":
    test_qwen2_5_coder_32b()
```

```bash
# 実行
python ~/workspace-ai/nomuraya-llm/model-qwen/test-qwen2.5-coder-32b.py
```

---

## コーディングエージェントとしての使用例

### 例1: コードレビュー

```bash
cat > /tmp/review-target.py <<'EOF'
def calculate_total(items):
    total = 0
    for item in items:
        total = total + item['price']
    return total
EOF

ollama run qwen2.5-coder:32b "以下のコードをレビューしてください: $(cat /tmp/review-target.py)"
```

### 例2: リファクタリング

```bash
ollama run qwen2.5-coder:32b "以下のコードをリファクタリングしてください: $(cat /tmp/review-target.py)"
```

### 例3: バグ修正

```bash
ollama run qwen2.5-coder:32b "以下のコードのバグを修正してください: $(cat /tmp/buggy-code.py)"
```

---

## トラブルシューティング

### エラー 1: メモリ不足

```
Error: out of memory
```

**対処**:
1. 他のアプリケーションを終了
2. PC再起動
3. Qwen2.5-Coder 7B に切り替え

---

### エラー 2: ダウンロード失敗

```
Error: failed to pull model
```

**対処**:
1. ディスク空き容量を確認
2. インターネット接続を確認
3. 再試行: `ollama pull qwen2.5-coder:32b`

---

### エラー 3: 起動が遅い

**症状**: `ollama run` が応答しない

**原因**: メモリスワップ発生

**対処**:
1. 即座に Ctrl+C で停止
2. `pkill -f ollama`
3. メモリ解放してから再試行

---

## ロールバック

### モデル削除

```bash
# Qwen2.5-Coder 32B を削除（19 GB 解放）
ollama rm qwen2.5-coder:32b

# 確認
ollama list | grep qwen2.5-coder:32b
# 何も表示されなければ削除成功
```

---

## 推奨運用

### Alfred (コーディングエージェント) の用途別モデル選択

| 用途 | 推奨モデル | 理由 |
|------|----------|------|
| コードレビュー | Qwen2.5-Coder 32B | 高精度 |
| 簡単なコード生成 | Qwen2.5-Coder 7B | 軽量、高速 |
| 複雑な設計判断 | DeepSeek-R1 32B | 推論能力 |

### メモリ監視の習慣化

```bash
# セッション開始時にメモリ確認
vm_stat | perl -ne '/page size of (\d+)/ and $size=$1; /Pages\s+([^:]+)[^\d]+(\d+)/ and printf("%-16s % 16.2f Mi\n", "$1:", $2 * $size / 1048576);' | grep free

# Free が 10 GB 以上なら 32B 実行可能
```

---

## DeepSeek-R1 32B との比較

| 項目 | Qwen2.5-Coder 32B | DeepSeek-R1 32B |
|------|------------------|-----------------|
| コード生成 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| 推論能力 | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| コンテキスト | 32K | 128K |
| HumanEval | 92.7% | 高（未公表） |
| 推奨用途 | コーディング | 設計判断 |

---

## 次のステップ

1. **PC再起動**
2. **このガイドに従ってセットアップ**
3. **軽量テストで動作確認**
4. **コーディングエージェントとして統合**

---

**作成日**: 2026-01-11
**作成者**: Atlas (Orchestrator)
**ステータス**: PC再起動後に実施
