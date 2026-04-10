Listen to an Announcement の新しい問題セットを作成してください。

## 事前準備（自動実行）
1. `docs/topic-history.md` を読み、Announcementセクションの使用済みトピックを確認する
2. `listening/announce/` フォルダ内の既存ファイルを確認する
3. 使用済みトピックと重複しない設定を2つ選定する

## 指定
$ARGUMENTS
- 例: `classroom library` → 指定された場面を使用
- 例: `random` or 空 → 全てランダム生成

## 出力ファイル（Phase 1）
1. `docs/scripts/announce-practice-{N}-scripts.md` — 録音用スクリプト＋問題＋Answer Key
2. `listening/announce/practice-{N}.html` — HTML（音声はプレースホルダー）

## Announcement 問題仕様

### 概要
- 2つのアナウンスメント × 各2問 = 計4問
- アナウンス長: 40-85語程度
- アナウンス再生中は質問非表示 → 終了後に質問表示+15秒タイマー

### 場面設定（以下から選択）
- "in a classroom" / "on the campus radio station" / "at a school event"
- "in a lecture hall" / "at a library"

### トピックカテゴリ
- スケジュール: 講義、イベント、締め切り
- 指示: 手続き、場所変更
- 規則: 新しいポリシー、注意事項
- 功績: 学生・教員の受賞、達成

### 問題タイプ
- Main Idea / Detail / Inference / Purpose から各2問を構成

### 正解分布
- A=1, B=1, C=1, D=1（均等）

### 声の割り当て
- Narrator: Rachel (American, Female) — 固定
- Speaker 1/2: 性別・アクセントを変えて配置

### HTML仕様
- 既存の listening-announce.html の構造を完全に踏襲
- Page 0: Instruction（音声自動再生、終了までNext無効）
- アナウンスページ: 左に話者画像、右に問題（音声終了後表示）
- 15秒タイマー（残5秒で赤色警告、0秒で自動次問題）
- 選択肢選択前はNext無効
- フッター: Check All Answers / Reset All / Show All Answers
- auth.js連携
- 話者画像: Base64埋め込み

### 作成後
1. スクリプト＋問題＋Answer Keyをmdファイルに出力
2. HTML雛形を作成
3. `docs/topic-history.md` のAnnouncementセクションに使用したトピックを追記
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
