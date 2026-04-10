Listen to a Conversation の新しい問題セットを作成してください。

## 事前準備（自動実行）
1. `docs/topic-history.md` を読み、Conversationセクションの使用済みトピックを確認する
2. `listening/conv/` フォルダ内の既存ファイルを確認する
3. 使用済みトピックと重複しない設定を2つ選定する

## 指定
$ARGUMENTS
- 例: `career center, chemistry lab` → 指定された設定を使用
- 例: `random` or 空 → 全てランダム生成

## 出力ファイル（Phase 1）
1. `docs/scripts/conv-practice-{N}-scripts.md` — 録音用スクリプト＋問題＋Answer Key
2. `listening/conv/practice-{N}.html` — HTML（音声はプレースホルダー）

## Conversation 問題仕様 (v1.0)

### 概要
- 2つの会話 × 各2問 = 計4問（Q9-Q12）
- 各会話: 30-40秒、6-10ターン、1ターン最大2文
- 音声再生中は画像のみ表示 → 終了後にSplit-panel（左:画像、右:問題）
- 各問題20秒カウントダウン

### 話者と設定パターン
- 学生 + 大学職員（書店、図書館、事務局、寮、ジム等）
- 学生 + 学生（授業後、カフェ、廊下、寮のロビー等）
- 学生 + 教授/TA（オフィスアワー、研究室等）

### 問題タイプ
- Q9: Detail (B1-B2) / Q10: Detail (B1-B2)
- Q11: Inference (B2) / Q12: Inference (B2)

### 正解分布
- A=1, B=1, C=1, D=1（均等）

### 選択肢ルール
- 各選択肢 5-12語、均一長
- ダッシュ/セミコロン禁止、文末ピリオド

### 声の割り当て
Conv 1とConv 2で異なるアクセントを使用:
- British: Alice (W) + Daniel (M)
- American: Sarah (W) + Chris (M)
- Australian: Nicole (W) + Callum (M)

### 会話画像
Google Driveフォルダの画像ストック4種から、設定に合った画像を選択:
- ① 青シャツ男性+ボーダーニット女性 → 学生同士カジュアル
- ② 黒スーツ女性+グレースーツ男性 → ビジネス/フォーマル
- ③ ラグランT男性+花柄ワンピ女性 → 学生同士カジュアル
- ④ カーディガン年配男性+ブラウス女性 → 教授+学生、職員+学生

### スクリプト作成ルール
- 各問題の正解根拠を会話内に明確に含める
- 不正解のひっかけ情報も自然に含める
- フィラーは最小限、感情表現は控えめ

### HTML仕様
- 既存の listening-conv.html の構造を完全に踏襲
- 音声再生中: 左パネル100%幅、画像センター、選択肢非表示
- 音声終了後: 左右50%分割、20秒カウントダウン
- 残り5秒で赤色点滅、0秒で自動次問題
- Start Listening オーバーレイ付き
- Q12のNext → listening-announce.html
- auth.js連携

### 作成後
1. スクリプト＋問題＋Answer Keyをmdファイルに出力
2. HTML雛形を作成
3. `docs/topic-history.md` のConversationセクションに使用したトピックを追記
4. 検証サマリーを表示

================================================================================
## ElevenLabs Voice ID リファレンス

### 音声生成手順
1. スクリプト確定後、各セリフを ElevenLabs TTS API で個別生成
2. レスポンスの MP3 を base64 エンコード
3. `data:audio/mpeg;base64,...` として HTML に埋め込み

### API 呼び出し
```
POST https://api.elevenlabs.io/v1/text-to-speech/{voice_id}
Headers:
  xi-api-key: $ELEVENLABS_API_KEY
  Content-Type: application/json
Body:
  {
    "text": "[スクリプト]",
    "model_id": "eleven_multilingual_v2",
    "voice_settings": {
      "stability": 0.6,
      "similarity_boost": 0.75,
      "style": 0
    }
  }
```

### Voice ID 一覧
| Name | Voice ID | Gender | Accent |
|------|----------|--------|--------|
| Rachel | 21m00Tcm4TlvDq8ikWAM | Female | American |
| Alice | Xb7hH8MSUJpSbSDYk0k2 | Female | British |
| Sarah | EXAVITQu4vr4xnSDxMaL | Female | American |
| Charlotte | XB0fDUnXU5powFXDhCwa | Female | British |
| Lily | pFZP5JQG7iQjIQuC4Bku | Female | British |
| Nicole | piTKgcLEGmPE4e6mEKli | Female | Australian |
| Matilda | XrExE9yKIg1WjnnlVkGX | Female | American |
| George | JBFqnCBsd6RMkjVDRZzb | Male | British |
| Daniel | onwK4e9ZLuTAKqWW03F9 | Male | British |
| Chris | iP95p4xoKVk53GoZ742B | Male | American |
| Callum | N2lVS1w4EtoT3dr4eOWO | Male | Australian |
| Liam | TX3LPaxmHKxFdv7VOQHJ | Male | American |
| Brian | nPczCjzI2devNBz1zQrb | Male | American |
| Joseph | Zlb1dXrM653N07WRdFW3 | Male | British |

### 重要ルール
- スクリプトに指定された Voice Name の Voice ID を使うこと
- 同じ画像には同じ声（同じ Voice ID）を使うこと
- base64 埋め込み後、HTML 内の audioFiles / convAudio 等のプレースホルダーを置換すること
