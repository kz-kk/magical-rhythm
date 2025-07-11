# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview
音楽解析リズムゲーム - ユーザーがアップロードした音楽ファイルを解析し、検出されたビートに合わせてプレイできるウェブベースの音ゲーアプリケーション

## Key Directories and Files
- `music-rhythm-game.html` - スタンドアロンHTML版の実装（Web Audio API、バニラJS）
- `MusicAnalyzerRhythmGame.txt` - Reactコンポーネント版の実装（React Hooks、Tailwind CSS）
- `.mcp.json` - MCP設定ファイル（AI画像・動画・音楽生成サービス連携）

## Development Commands
### HTMLバージョン
- **実行方法**: ブラウザで`music-rhythm-game.html`を直接開く
- **テスト方法**: ブラウザのDevToolsコンソールでエラーを確認
- **デバッグ**: `console.log`でビート検出データを確認

### Reactバージョン
- **実行前提**: 親プロジェクトにインポートして使用
- **必要な依存関係**: `react`, `lucide-react`, `tailwindcss`

## Code Architecture
### ゲーム状態管理
- `gameState`オブジェクトで全ゲーム状態を管理
- 主要な状態: `isPlaying`, `isPaused`, `score`, `combo`, `beatTimes`, `difficulty`

### 音楽解析システム
- Web Audio APIを使用したRMS（Root Mean Square）ベースのビート検出
- `analyzeBeats()`関数で音楽データを解析し、ビートタイミングを抽出
- フォールバック: 解析失敗時は120BPMのシンプルパターンを生成

### ゲームロジック
- 4レーン（キー: 1,2,3,4 / タッチ: 画面4分割）
- 判定システム: PERFECT (50ms以内), GOOD (100ms以内), MISS
- ノート落下速度: 300px/秒（`NOTE_SPEED`定数）
- 判定ライン位置: Y座標350px（`JUDGMENT_LINE_Y`定数）

### 難易度システム
- Easy: ビート数を50%に削減（偶数番目のビートのみ使用）
- Hard: 全ビートを使用

## Important Implementation Details
### 音楽ファイル対応形式
- MP3, WAV, OGG, M4A（`accept="audio/*"`で幅広く対応）

### ビート検出アルゴリズム
1. 音楽データをチャンクに分割（44100サンプル = 1秒）
2. 各チャンクのRMS値を計算
3. 動的閾値（平均RMS × 1.2）を超えるピークを検出
4. 最小間隔300msでフィルタリング

### レスポンシブ対応
- タッチデバイス: 画面を4分割してレーン判定
- キーボード: 1,2,3,4キーでレーン操作
- スペースキー: 一時停止/再開

### パフォーマンス最適化
- `requestAnimationFrame`でゲームループを実装
- ノートは画面外に出たら自動削除
- 音楽解析は非同期処理で実行

## Testing Approach
- ブラウザのDevToolsでエラーログを確認
- 異なる音楽ファイル形式でテスト
- タッチ操作とキーボード操作の両方を確認
- 難易度切り替えの動作確認

## Common Patterns
### イベントハンドリング
```javascript
element.addEventListener('event', (e) => {
    e.preventDefault();
    // 処理
});
```

### 状態更新パターン
```javascript
gameState.property = value;
updateUI(); // UI反映関数を呼び出す
```

### エラーハンドリング
```javascript
try {
    // 処理
} catch (error) {
    console.error('エラー内容:', error);
    // フォールバック処理
}
```