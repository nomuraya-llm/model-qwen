# Qwen 2.5

**Qwen2.5**は、Alibaba Cloud（阿里巴巴）が開発した標準的で汎用性に優れたAIモデルです。ビジネス応用に向け、安定した性能と充実した言語対応を備えています。

## 概要

- **パラメータ数**: 7B/72B
- **特徴**: 標準的な性能、ビジネス向け、充実した言語対応
- **開発元**: Alibaba Cloud
- **日本語対応**: 公式対応
- **コンテキスト長**: 128k トークン
- **ライセンス**: Qwen License

## スペック比較

| 項目 | 7B版 | 72B版 |
|------|-------|--------|
| パラメータ数 | 7B | 72B |
| メモリ要件 | 8GB+ | 64GB+ |
| 推論速度 | 非常に高速 | 中程度 |
| 言語対応 | 29言語 | 29言語 |
| コンテキスト | 128k | 128k |

## クイックスタート

### 方法1: GGUF版（llamacpp）

このリポジトリにGGUFファイルを配置して使用します。

**ローカル配置済みモデル**:
- qwen2.5-0.5b-instruct-q4_k_m.gguf (469MB)
- qwen2.5-1.5b-instruct-q4_k_m.gguf (1.0GB)

**ダウンロード可能なモデル**:
```bash
# Qwen2.5 3B Q4_K_M (2.0GB)
wget https://huggingface.co/Qwen/Qwen2.5-3B-Instruct-GGUF/resolve/main/qwen2.5-3b-instruct-q4_k_m.gguf
```

**注意**: `*.gguf`ファイルはGit管理外（`.gitignore`で除外）

### 方法2: Ollama経由のインストール

```bash
# 7B版（推奨）
ollama pull qwen2.5:7b

# 72B版（高精度）
ollama pull qwen2.5:72b
```

### 実行

```bash
ollama run qwen2.5:7b
```

## 推奨用途

- **一般的な質問応答**
- **ビジネス文書作成**
- **コンテンツ生成**
- **多言語対応が必要なタスク**
- **バランスの取れた性能が必要な場合**

## 特徴

- **安定した性能**: 多くのベンチマークで高スコア
- **充実した言語対応**: 29言語をネイティブレベルでサポート
- **長いコンテキスト**: 128k トークンで長文処理に対応
- **商用利用**: ビジネス環境での利用を想定

## 関連リンク

- [Qwen公式サイト](https://qwenlm.github.io/)
- [HuggingFace (Alibaba-NLP)](https://huggingface.co/Alibaba-NLP)
- [Ollama - Qwen](https://ollama.ai/library/qwen2.5)

---

生成日: 2026-01-09
