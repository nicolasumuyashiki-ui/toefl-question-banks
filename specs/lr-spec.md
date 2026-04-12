Listen and Repeat の新しい問題セットを作成してください。

## 事前準備（自動実行）
1. `docs/topic-history.md` を読み、Listen and Repeatセクションの使用済みトピックを確認する
2. `speaking/lr/` フォルダ内の既存ファイルを確認する
3. 使用済みトピックと重複しないシナリオを選定する

## 指定
$ARGUMENTS
- 例: `museum guide training` → 指定されたシナリオを使用
- 例: `random` or 空 → 全てランダム生成

## 出力ファイル（Phase 1）
1. `docs/scripts/lr-practice-{N}-scripts.md` — 録音用スクリプト＋Scenario＋全7文
2. `speaking/lr/practice-{N}.html` — HTML（音声・画像はプレースホルダー）
3. Ideogram用画像プロンプト（サマリーに出力）

## Listen and Repeat 問題仕様

### 概要
- 7文を聞いて正確に繰り返す
- 文は段階的に長く・複雑になる
- 各文に8-12秒の回答時間

### 自動進行フロー
Start → Scenario音声 → 2秒待機 → Q1音声 → 2秒 → カウントダウン → Q2... → Q7 → Complete

### 文の長さ（段階的増加）
| Q | 語数 | 制限時間 |
|---|------|---------|
| Q1 | 4-6語 | 8秒 |
| Q2 | 6-8語 | 8秒 |
| Q3 | 8-10語 | 9秒 |
| Q4 | 10-12語 | 10秒 |
| Q5 | 12-14語 | 10秒 |
| Q6 | 14-17語 | 11秒 |
| Q7 | 17-20語 | 12秒 |

### シナリオ設定
- アカデミックまたはキャンパスライフの場面
- 新人研修、ガイドツアー、オリエンテーション等
- 視覚的に表現しやすいテーマ

### 文の特徴
- 自然な英語
- キャンパスや職場で実際に使われる表現
- 複雑な文法構造は後半に配置
- 固有名詞や数字を適度に含める

### 声の指定
- Scenario + Q1-Q7: 全て同じTrainer役の声で統一
- アクセント: American / British / Australian / NZ のいずれか

### HTML仕様
- 既存の lr-1.html の構造を完全に踏襲
- 説明ページ: タスク説明表＋Start ボタン
- タスクページ: Scenarioボックス＋画像（400×400px）＋タイマー
- タイマー: 黒背景中央配置、「RESPONSE TIME」ラベル、録音アイコン、水色カウントダウン
- 音声ファイル設定: scenario.mp3, q1.mp3-q7.mp3（外部参照プレースホルダー）
- 画像: scenarioImage（プレースホルダー）
- auth.js連携

### Ideogram画像生成（自動 — 環境変数 IDEOGRAM_API_KEY を使用）

スクリプト完成後、Ideogram API でシナリオ画像を生成し、base64でHTMLに埋め込むこと。

#### プロンプト生成ルール（厳守）
1. **全7文のスクリプトを読み取る** — 各文の内容を忠実に反映した画像にすること
2. **位置関係を正確に再現** — "On the right, there is a..." なら画像の右側にその物を配置
3. **スタイル: children's book** — 必ず "children's book illustration style" を指定
4. **テキスト禁止** — プロンプトの**冒頭と末尾の両方**に "Absolutely no text, no words, no letters, no numbers, no labels, no signs, no writing of any kind." を含める
5. **1:1 アスペクト比**
6. **各文に対応する視覚要素を明示的に列挙** — 曖昧な表現（"various activities" 等）は使わず、具体的な人物・物・動作を書く

#### プロンプトのテンプレート
```
Children's book illustration style. Absolutely no text, no writing, no labels, no signs anywhere.
A warm, colorful scene of [場所の具体的な説明].
[Q1: 具体的な人物・物・動作の描写].
[Q2: 具体的な人物・物・動作の描写].
[Q3: 具体的な人物・物・動作の描写].
[Q4: 具体的な人物・物・動作の描写].
[Q5: 具体的な人物・物・動作の描写].
[Q6: 具体的な人物・物・動作の描写].
[Q7: 具体的な人物・物・動作の描写].
Soft warm pastel colors, friendly educational atmosphere.
Absolutely no text, no words, no letters, no numbers, no labels, no signs, no writing of any kind.
```

#### API呼び出し
```
POST https://api.ideogram.ai/generate
Headers:
  Api-Key: $IDEOGRAM_API_KEY
  Content-Type: application/json
Body:
  {
    "image_request": {
      "prompt": "[生成したプロンプト]",
      "aspect_ratio": "ASPECT_1_1",
      "style_type": "GENERAL",
      "magic_prompt_option": "ON",
      "model": "V_2"
    }
  }
```

#### 生成後の検証ループ（必須）
画像生成後、以下の7項目チェックを実施する。**1つでもNGがあればプロンプトを修正して再生成**すること。最大3回まで再試行する。

**チェックリスト（全項目 ✅ になるまで繰り返す）:**
- [ ] Q1 の内容に対応する視覚要素が画像に含まれているか
- [ ] Q2 の内容に対応する視覚要素が画像に含まれているか
- [ ] Q3 の内容に対応する視覚要素が画像に含まれているか
- [ ] Q4 の内容に対応する視覚要素が画像に含まれているか
- [ ] Q5 の内容に対応する視覚要素が画像に含まれているか
- [ ] Q6 の内容に対応する視覚要素が画像に含まれているか
- [ ] Q7 の内容に対応する視覚要素が画像に含まれているか

**再生成時のプロンプト修正方法:**
1. 欠落した要素を特定する
2. その要素の描写をプロンプト内でより具体的・詳細に書き直す
3. 他の要素と空間的に分離して配置する（例: "In the top-left corner..." "In the bottom-right..."）
4. 再生成して再チェック

**3回再生成してもNGの場合:** 最も多くの要素を含む画像を採用し、欠落要素をログに記録する。

レスポンスの `data[0].url` から画像をダウンロードし、base64エンコードして `scenarioImage` に埋め込む。

### 成果物一覧（3段階ワークフロー）
| ステップ | 成果物 | 説明 |
|---------|--------|------|
| ① Claude | practice-{N}.html | HTMLファイル（音声・画像未埋め込み） |
| ① Claude | docs/scripts/lr-practice-{N}-scripts.md | 録音用スクリプト |
| ①-B Ideogram | シナリオ画像（PNG） | シナリオに合ったイラスト |
| ② ElevenLabs | scenario.mp3, q1-q7.mp3 | 音声ファイル（計8ファイル） |
| ③ Claude | practice-{N}.html（完成版） | 音声・画像をBase64埋め込み済み |

### ElevenLabs音声設定
- 声: 全て同じTrainer役で統一
- Stability: 0.5 - 0.7
- Clarity + Similarity Enhancement: 0.7 - 0.8
- Style: 0（ニュートラル）
- NarratorとTrainerは同一の声でOK（LRの場合）

### 検証チェックリスト
- [ ] 説明ページが表示される
- [ ] Startボタンでタスク開始
- [ ] Scenarioボックスにテキストが表示される
- [ ] Scenario → Q1 → Q2 → ... → Q7 → Complete と自動進行
- [ ] 各Question音声が再生される
- [ ] 「Playing audio...」インジケーターが表示される
- [ ] 音声終了から2秒後にカウントダウン開始
- [ ] RESPONSE TIMEラベル＋録音アイコン（🎙️）が表示される
- [ ] カウントダウンが正しく動作する（Q1: 8秒 → Q7: 12秒）
- [ ] シナリオ画像が中央に表示される（400×400px）
- [ ] 画像にテキスト・数字が含まれていない
- [ ] Q1-Q7の語数が仕様範囲内

### 作成後
1. スクリプトを `docs/scripts/lr-practice-{N}-scripts.md` に保存
2. HTML雛形を作成（音声・画像はプレースホルダー）
3. Ideogram用画像プロンプトを出力
4. `docs/topic-history.md` のListen and Repeatセクションに使用したトピックを追記
5. 検証サマリー: 語数チェック（Q1-Q7が仕様範囲内か）

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
