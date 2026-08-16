# 3tiles<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MATCH-3 PUZZLE - FOREST PARTY STORY</title>
  <link href="https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@700;800;900&display=swap" rel="stylesheet">
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'M PLUS Rounded 1c', sans-serif;
    }

    body {
      background: #ffeef8;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 10px;
      overflow: auto;
      user-select: none;
    }

    /* スマホ版表示モード：全体を自動縮小してスクロール不要にする */
    body.mobile-active {
      overflow: hidden;
      height: 100vh;
    }

    .game-container {
      display: flex;
      gap: 18px;
      align-items: stretch;
      justify-content: center;
      max-width: 1320px;
      width: 100%;
    }

    /* スマホ表示モード：左右パネルとメインボードを縦に積んで見やすく */
    .game-container.layout-mobile {
      flex-direction: column;
      align-items: center;
      transform-origin: top center;
    }

    .game-container.layout-mobile .panel,
    .game-container.layout-mobile .main-board {
      width: 100%;
      max-width: 462px;
    }

    .panel {
      background: #ffffff;
      border-radius: 24px;
      padding: 18px;
      box-shadow: 0 4px 15px rgba(255, 182, 193, 0.4);
      display: flex;
      flex-direction: column;
      gap: 12px;
      width: 253px;
      border: 3px solid #ffdeeb;
    }

    .card {
      background: #fff8fa;
      border-radius: 16px;
      padding: 10px;
      text-align: center;
      border: 2px solid #ffeef2;
    }

    .card-title {
      font-size: 13px;
      color: #ff85a2;
      font-weight: 800;
      margin-bottom: 4px;
    }

    .card-value {
      font-size: 28px;
      font-weight: 900;
      color: #ff6b8b;
    }

    .card-value.blue {
      color: #2ed573;
    }

    .btn {
      background: #ff9aa2;
      color: #fff;
      border: none;
      padding: 12px 16px;
      border-radius: 20px;
      font-weight: 900;
      font-size: 16px;
      cursor: pointer;
      box-shadow: 0 4px 0 #ff6b8b;
      transition: transform 0.05s, background-color 0.1s;
    }

    .btn:active {
      transform: translateY(2px);
      box-shadow: 0 2px 0 #ff6b8b;
    }

    .btn-assist {
      background: #70a1ff;
      box-shadow: 0 4px 0 #1e90ff;
    }

    .btn-shuffle {
      background: #ff7f50;
      box-shadow: 0 4px 0 #e056fd;
    }

    .btn-secondary {
      background: #eccc68;
      box-shadow: 0 4px 0 #ffa502;
      color: #ffffff;
    }

    .timer-circle {
      width: 60px;
      height: 60px;
      border-radius: 50%;
      border: 3px solid #ffccd5;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      margin: 0 auto;
      background: #fff5f7;
    }

    .timer-circle.hurry {
      border-color: #ff4757;
      background: #ffe0e3;
    }

    .timer-value {
      font-size: 22px;
      font-weight: 900;
      color: #ff6b8b;
    }

    .game-title {
      font-size: 19px;
      font-weight: 900;
      color: #ff6b8b;
      text-align: center;
    }

    .mascot-area {
      display: flex;
      align-items: center;
      gap: 8px;
      background: #fff0f5;
      border: 2px dashed #ffb6c1;
      border-radius: 18px;
      padding: 10px 12px;
    }

    .mascot-avatar {
      font-size: 37px;
      width: 48px;
      height: 48px;
      flex-shrink: 0;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .mascot-avatar svg {
      width: 100%;
      height: 100%;
      display: block;
    }

    .mascot-speech {
      font-size: 12px;
      font-weight: 900;
      color: #ff4757;
      line-height: 1.4;
      background: #ffffff;
      padding: 6px 10px;
      border-radius: 12px;
      border: 1px solid #ffd1dc;
      flex: 1;
    }

    .main-board {
      background: #ffffff;
      border-radius: 28px;
      padding: 18px;
      box-shadow: 0 6px 20px rgba(255, 182, 193, 0.4);
      display: flex;
      flex-direction: column;
      align-items: center;
      position: relative;
      border: 4px solid #ffdeeb;
    }

    .puzzle-grid {
      position: relative;
      width: 462px;
      height: 554px;
      background: #fff5f8;
      border-radius: 20px;
      border: 2px dashed #ffccd5;
      overflow: hidden;
    }

    .tile {
      position: absolute;
      width: 79px;
      height: 79px;
      border-radius: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 40px;
      cursor: grab;
      touch-action: none;
      top: 0;
      left: 0;
      will-change: transform;
      border: 2px solid rgba(255, 255, 255, 0.6);
      transition: transform 0.18s cubic-bezier(0.25, 1, 0.5, 1);
    }

    .tile.selected {
      border: 4px solid #ff4757;
      z-index: 10;
    }

    .tile.hint {
      border: 4px dashed #1e90ff;
      z-index: 5;
    }

    .tile-apple  { background: #ffb3ba; }
    .tile-tree   { background: #baffc9; }
    .tile-water  { background: #bae1ff; }
    .tile-star   { background: #ffffba; }
    .tile-brown  { background: #d3a27f; }

    /* ストーリー＆スタート画面（難易度一括選択つき） */
    .start-overlay {
      position: absolute;
      inset: 0;
      background: rgba(255, 240, 245, 0.98);
      border-radius: 28px;
      display: flex;
      flex-direction: column;
      gap: 10px;
      align-items: center;
      justify-content: flex-start;
      z-index: 20;
      padding: 18px;
      text-align: center;
      overflow-y: auto;
    }

    /* 開始画面のかわいいタイトル表記 */
    .overlay-title {
      font-size: 24px;
      font-weight: 900;
      color: #ff6b8b;
      letter-spacing: 1px;
      text-shadow: 2px 2px 0 #fff, 0 0 10px rgba(255, 182, 193, 0.6);
      margin-bottom: 2px;
    }

    /* 作者クレジット：右下に小さく、丸文字でかわいく */
    .credit-tag {
      position: absolute;
      right: 16px;
      bottom: 10px;
      font-size: 11px;
      font-weight: 800;
      color: #ffb0c9;
      letter-spacing: 0.5px;
      background: #fff5f8;
      padding: 3px 10px;
      border-radius: 999px;
      border: 1px solid #ffd9e4;
    }

    .story-card {
      background: #ffffff;
      border: 3px solid #ffb6c1;
      border-radius: 20px;
      padding: 16px;
      max-width: 429px;
      width: 100%;
      box-shadow: 0 4px 14px rgba(255, 182, 193, 0.3);
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    .story-card-title {
      font-size: 16px;
      font-weight: 900;
      color: #ff6b8b;
    }

    .story-card-body {
      font-size: 13px;
      font-weight: 800;
      color: #555;
      line-height: 1.5;
    }

    .story-card-footer {
      background: #fff0f5;
      border-radius: 12px;
      padding: 8px;
      font-size: 13px;
      font-weight: 900;
      color: #ff4757;
    }

    .difficulty-select-area {
      background: #ffffff;
      border: 2px solid #ffccd5;
      border-radius: 16px;
      padding: 10px;
      width: 100%;
      max-width: 429px;
    }

    .difficulty-select {
      display: flex;
      gap: 6px;
      width: 100%;
      justify-content: center;
      margin-top: 4px;
    }

    .diff-btn {
      flex: 1;
      padding: 8px 4px;
      font-size: 13px;
      border-radius: 12px;
      background: #fff5f8;
      color: #ff6b8b;
      border: 2px solid #ffccd5;
      cursor: pointer;
      font-weight: 900;
      transition: all 0.1s;
    }

    .diff-btn.active {
      background: #ff6b8b;
      color: #fff;
      border-color: #ff4757;
      box-shadow: 0 3px 0 #ff4757;
    }

    .result-modal {
      display: none;
      position: absolute;
      inset: 0;
      background: rgba(255, 240, 245, 0.96);
      border-radius: 28px;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      z-index: 30;
      padding: 20px;
    }

    .result-card {
      background: #ffffff;
      padding: 22px;
      border-radius: 20px;
      text-align: center;
      box-shadow: 0 8px 20px rgba(255, 182, 193, 0.4);
      width: 90%;
      max-width: 330px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 12px;
      border: 2px solid #ffeef2;
    }

    .result-title {
      font-size: 24px;
      font-weight: 900;
      color: #ff6b8b;
    }

    .clear-status {
      font-size: 19px;
      font-weight: 900;
      padding: 6px 16px;
      border-radius: 20px;
    }
    .clear-status.success { background: #2ed573; color: white; }
    .clear-status.fail { background: #ff4757; color: white; }

    .score-count {
      font-size: 34px;
      font-weight: 900;
      color: #ff6b8b;
    }

    #effect-canvas {
      position: absolute;
      inset: 0;
      width: 100%;
      height: 100%;
      pointer-events: none;
      z-index: 25;
    }

    .float-text {
      position: absolute;
      font-weight: 900;
      font-size: 26px;
      color: #ff4757;
      pointer-events: none;
      z-index: 24;
      will-change: transform, opacity;
    }
  </style>
</head>
<body>

<div class="game-container">

  <!-- 【左パネル】タイトル, マスコット, 💡ヒント, 🔀シャッフル, ハイスコア, 🎵BGM -->
  <div class="panel">
    <div class="game-title">✨ FOREST PARTY ✨</div>
    
    <div class="mascot-area">
      <div class="mascot-avatar" id="mascot-avatar">🐰</div>
      <div class="mascot-speech" id="mascot-speech">パーティーの準備を楽しもう！</div>
    </div>

    <button class="btn btn-assist" id="hint-btn" onclick="useAssistHint()">💡 ヒント</button>
    <button class="btn btn-shuffle" id="shuffle-btn" onclick="shuffleBoard(true)">🔀 シャッフル</button>

    <div class="card" style="margin-top: auto;">
      <div class="card-title">🏆 ハイスコア</div>
      <div class="card-value" id="highscore">500</div>
    </div>

    <button class="btn btn-secondary" id="bgm-btn" onclick="toggleBGM()">🎵 BGM: ON</button>
  </div>

  <!-- 【中央パネル】メインボード / 物語画面・スタート / リザルト画面 -->
  <div class="main-board" id="board-container">
    <canvas id="effect-canvas"></canvas>
    <div class="puzzle-grid" id="grid"></div>

    <!-- 起動時の最初の画面：タイトル・作者名・表示モード選択のみ -->
    <div class="start-overlay" id="landing-overlay">
      <div class="overlay-title">🌲 森のパーティー 🌲</div>

      <div class="difficulty-select-area">
        <div class="card-title" style="margin-bottom: 2px;">🖥️ 表示モードを選んでね</div>
        <div class="difficulty-select">
          <button class="diff-btn" id="layout-pc" onclick="chooseLayout('pc')">💻 PC版</button>
          <button class="diff-btn" id="layout-mobile" onclick="chooseLayout('mobile')">📱 スマホ版</button>
        </div>
      </div>

      <div class="credit-tag">✨ Flour shohei ✨</div>
    </div>

    <!-- ストーリー＆スタート画面（表示モード選択後に表示） -->
    <div class="start-overlay" id="start-overlay" style="display: none;">
      <div class="story-card">
        <div style="font-size: 32px;" id="story-avatar">📖🐰🍎</div>
        <div class="story-card-title" id="story-title">ステージ 1：森でくだもの集め</div>
        <div class="story-card-body" id="story-desc">
          「森のみんなで盛大なパーティーを開くことになったよ！まずはみんなで食べるための新鮮なフルーツをたくさん集めよう！」
        </div>
        <div class="story-card-footer" id="story-target">
          🎯 目標スコア: 150 pt
        </div>
      </div>

      <!-- ゲームモード選択：ストーリーモード（順番にクリア）／フリーモード（好きなステージを選択） -->
      <div class="difficulty-select-area">
        <div class="card-title" style="margin-bottom: 2px;">🎮 モード</div>
        <div class="difficulty-select">
          <button class="diff-btn active" id="mode-story" onclick="setGameMode('story')">📖 ストーリー</button>
          <button class="diff-btn" id="mode-free" onclick="setGameMode('free')">🎲 フリー</button>
        </div>
      </div>

      <!-- 難易度一括選択（ステージに関わらず全体に適用、タイル種類数は非表示） -->
      <div class="difficulty-select-area">
        <div class="card-title" style="margin-bottom: 2px;">🧩 むずかしさ</div>
        <div class="difficulty-select" id="difficulty-select-buttons">
          <button class="diff-btn" id="diff-easy" onclick="setGlobalDifficulty('easy')">🌸 かんたん</button>
          <button class="diff-btn active" id="diff-normal" onclick="setGlobalDifficulty('normal')">⭐ ふつう</button>
          <button class="diff-btn" id="diff-hard" onclick="setGlobalDifficulty('hard')">🔥 むずかしい</button>
        </div>
      </div>

      <button class="btn" id="start-btn" onclick="startGame()" style="font-size: 18px; padding: 12px 30px;">🚀 このステージをはじめる！</button>
    </div>

    <!-- リザルト画面 -->
    <div class="result-modal" id="result-modal">
      <div class="result-card">
        <div class="result-title" id="result-title">🍓 ステージ結果 🍓</div>
        <div class="clear-status success" id="clear-status">STAGE CLEAR!</div>
        
        <div>
          <div style="font-size: 13px; color: #a0a0a0; font-weight: bold;">獲得スコア</div>
          <div class="score-count" id="result-score">0</div>
        </div>

        <div style="font-size: 12px; color: #555; font-weight: 900;" id="result-message">
          目標達成！次のステージへ移動します！
        </div>

        <button class="btn" id="next-btn" onclick="nextStageOrRetry()" style="width: 100%; margin-top: 6px;">つぎのステージへ進む ➔</button>
      </div>
    </div>
  </div>

  <!-- 【右パネル】制限時間, SCORE, 連鎖数, 消した数, やりなおす -->
  <div class="panel">
    <div class="card">
      <div class="card-title">⏳ 制限時間</div>
      <div class="timer-circle" id="timer-box">
        <span class="timer-value" id="timer">60</span>
        <span style="font-size: 10px; color: #ff6b8b; font-weight: bold;">秒</span>
      </div>
    </div>

    <div class="card">
      <div class="card-title">⭐ SCORE</div>
      <div class="card-value"><span id="score">0</span><span style="font-size: 15px; color: #ff9aa2;"> / <span id="target-display">150</span></span></div>
    </div>

    <div class="card">
      <div class="card-title">✨ 連鎖数</div>
      <div class="card-value blue" id="combo">0</div>
    </div>

    <div class="card">
      <div class="card-title">🍒 消した数</div>
      <div class="card-value blue" id="cleared">0</div>
    </div>

    <button class="btn" style="margin-top: auto;" onclick="resetGame()">やりなおす</button>
  </div>

</div>

<script>
  const COLS = 5;
  const ROWS = 6;
  const GAME_TIME = 60;
  const TILE_SIZE = 84;
  const OFFSET = 11;

  // ストーリー＆ステージ構成（難易度ごとにクリア目標スコアを設定・全体的に簡単に調整）
  const stages = [
    {
      id: 1,
      title: "ステージ 1：森でくだもの集め",
      desc: "「森のみんなで盛大なパーティーを開くことになったよ！まずはみんなで食べるための新鮮なフルーツをたくさん集めよう！」",
      targetScore: { easy: 60, normal: 90, hard: 130 },
      avatar: '📖🐰🍎🍇',
      mascotKey: 'rabbit',
      mascotStart: "フルーツを集めよう！"
    },
    {
      id: 2,
      title: "ステージ 2：きのみとジュース準備",
      desc: "「食材がたくさん集まったね！次は特製ジュースを作ったり、パーティー会場の飾り付けを進めよう！」",
      targetScore: { easy: 140, normal: 200, hard: 280 },
      avatar: '📖🐿️🍹🌰',
      mascotKey: 'squirrel',
      mascotStart: "準備を完了させよう！"
    },
    {
      id: 3,
      title: "ステージ 3：みんなで大パーティー！",
      desc: "「いよいよ待ちに待ったパーティーの始まり！森中の動物たちを呼んで、最高の盛り上がりを見せよう！」",
      targetScore: { easy: 240, normal: 340, hard: 440 },
      avatar: '📖🐻🎂🎉',
      mascotKey: 'bear',
      mascotStart: "達成で伝説のパーティーに！"
    }
  ];

  let currentStageIndex = 0;
  let globalDifficulty = 'normal'; // 最初に一括選択する難易度 (easy / normal / hard)
  let gameMode = 'story'; // story: 順番にステージクリア／free: ステージ選択なし・難易度だけ選んでハイスコアを狙う
  let prevHighScoreForRun = 0; // フリーモードで「今回の記録が更新か」を判定するための開始時ハイスコア

  function getCurrentTargetScore() {
    return stages[currentStageIndex].targetScore[globalDifficulty];
  }

  function setGameMode(mode) {
    playCuteSE('click');
    gameMode = mode;
    document.getElementById('mode-story').classList.toggle('active', mode === 'story');
    document.getElementById('mode-free').classList.toggle('active', mode === 'free');
    updateStageUI();
  }

  // PC版／スマホ版の表示レイアウトを切り替える
  // 表示モードを選んで、ランディング画面 → ゲーム設定画面へ切り替える
  function chooseLayout(mode) {
    playCuteSE('click');
    document.getElementById('layout-pc').classList.toggle('active', mode === 'pc');
    document.getElementById('layout-mobile').classList.toggle('active', mode === 'mobile');

    const container = document.querySelector('.game-container');
    container.classList.toggle('layout-mobile', mode === 'mobile');
    document.body.classList.toggle('mobile-active', mode === 'mobile');

    document.getElementById('landing-overlay').style.display = 'none';
    document.getElementById('start-overlay').style.display = 'flex';

    if (mode === 'mobile') {
      requestAnimationFrame(fitMobileLayout);
    } else {
      container.style.transform = '';
    }
  }

  // スマホ版では、画面をスクロールしなくても収まるよう全体を自動で縮小表示する
  function fitMobileLayout() {
    const container = document.querySelector('.game-container');
    if (!container.classList.contains('layout-mobile')) return;

    container.style.transform = 'none';
    const rect = container.getBoundingClientRect();
    const availW = window.innerWidth - 16;
    const availH = window.innerHeight - 16;
    const scale = Math.min(availW / rect.width, availH / rect.height, 1);
    container.style.transform = `scale(${scale})`;
  }

  window.addEventListener('resize', () => {
    if (document.body.classList.contains('mobile-active')) fitMobileLayout();
  });

  const allTileTypes = [
    { id: 0, type: 'tile-apple', icon: '🍎', color: '#ffb3ba' },
    { id: 1, type: 'tile-tree', icon: '🐥', color: '#baffc9' },
    { id: 2, type: 'tile-water', icon: '💧', color: '#bae1ff' },
    { id: 3, type: 'tile-star', icon: '⭐', color: '#ffffba' },
    { id: 4, type: 'tile-brown', icon: '🟫', color: '#d3a27f' }
  ];

  function setGlobalDifficulty(diff) {
    playCuteSE('click');
    globalDifficulty = diff;
    document.querySelectorAll('#difficulty-select-buttons .diff-btn').forEach(btn => btn.classList.remove('active'));
    document.getElementById(`diff-${diff}`).classList.add('active');
    updateStageUI();
  }

  function getActiveTileTypes() {
    if (globalDifficulty === 'easy') return allTileTypes.slice(0, 3);
    if (globalDifficulty === 'normal') return allTileTypes.slice(0, 4);
    return allTileTypes.slice(0, 5);
  }

  function getScoreMultiplier() {
    // 目標スコアを2倍にしたのに合わせ、獲得倍率も引き締めて全体の難易度を底上げ
    const baseMult = 0.05;
    if (globalDifficulty === 'easy') return baseMult * 1.0;
    if (globalDifficulty === 'normal') return baseMult * 0.9;
    return baseMult * 0.8;
  }

  let board = [];
  let selectedTileIndex = null;
  let activeHintIndices = [];
  let isProcessing = false;
  let isPlaying = false;
  
  let score = 0;
  let clearedCount = 0;
  let highScore = 500;
  let timeLeft = GAME_TIME;
  let timerInterval = null;

  let audioCtx = null;
  let isBgmOn = true;
  let currentBgmPhase = 0;
  let bgmTimer = null;

  function initAudio() {
    if (!audioCtx) {
      audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    }
    if (audioCtx.state === 'suspended') {
      audioCtx.resume();
    }
    if (currentBgmPhase === 0 && isBgmOn) {
      setBGMPhase(5);
    }
  }

  window.addEventListener('load', () => {
    initAudio();
    updateStageUI();
  });
  window.addEventListener('pointerdown', initAudio, { once: true });

  // かわいい動物SVGアイコン（絵文字が「?」表示になる環境でも崩れないように画像化）
  const mascotSVGs = {
    rabbit: `<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
      <ellipse cx="34" cy="20" rx="11" ry="26" fill="#fff" stroke="#ffb6c1" stroke-width="3"/>
      <ellipse cx="66" cy="20" rx="11" ry="26" fill="#fff" stroke="#ffb6c1" stroke-width="3"/>
      <ellipse cx="34" cy="22" rx="5.5" ry="17" fill="#ffd1dc"/>
      <ellipse cx="66" cy="22" rx="5.5" ry="17" fill="#ffd1dc"/>
      <circle cx="50" cy="62" r="36" fill="#fff" stroke="#ffb6c1" stroke-width="3"/>
      <circle cx="38" cy="58" r="5.2" fill="#5a4632"/>
      <circle cx="62" cy="58" r="5.2" fill="#5a4632"/>
      <circle cx="40" cy="56" r="1.8" fill="#fff"/>
      <circle cx="64" cy="56" r="1.8" fill="#fff"/>
      <circle cx="30" cy="70" r="5.5" fill="#ffb6c1" opacity="0.65"/>
      <circle cx="70" cy="70" r="5.5" fill="#ffb6c1" opacity="0.65"/>
      <circle cx="50" cy="67" r="2.6" fill="#ff8fa3"/>
      <path d="M45 71 Q50 76 55 71" stroke="#5a4632" stroke-width="2.2" fill="none" stroke-linecap="round"/>
    </svg>`,
    squirrel: `<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
      <circle cx="28" cy="26" r="14" fill="#d3a27f" stroke="#a9744f" stroke-width="3"/>
      <circle cx="72" cy="26" r="14" fill="#d3a27f" stroke="#a9744f" stroke-width="3"/>
      <circle cx="28" cy="26" r="7" fill="#fbe4cf"/>
      <circle cx="72" cy="26" r="7" fill="#fbe4cf"/>
      <circle cx="50" cy="60" r="35" fill="#e8bb90" stroke="#a9744f" stroke-width="3"/>
      <ellipse cx="50" cy="68" rx="17" ry="13" fill="#fff6ec"/>
      <circle cx="39" cy="56" r="5.2" fill="#5a4632"/>
      <circle cx="61" cy="56" r="5.2" fill="#5a4632"/>
      <circle cx="41" cy="54" r="1.8" fill="#fff"/>
      <circle cx="63" cy="54" r="1.8" fill="#fff"/>
      <circle cx="31" cy="68" r="5" fill="#ffb6c1" opacity="0.6"/>
      <circle cx="69" cy="68" r="5" fill="#ffb6c1" opacity="0.6"/>
      <circle cx="50" cy="65" r="2.8" fill="#7a4a2f"/>
      <path d="M45 71 Q50 75 55 71" stroke="#5a4632" stroke-width="2" fill="none" stroke-linecap="round"/>
    </svg>`,
    bear: `<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
      <circle cx="26" cy="24" r="15" fill="#c8996b" stroke="#8b5e34" stroke-width="3"/>
      <circle cx="74" cy="24" r="15" fill="#c8996b" stroke="#8b5e34" stroke-width="3"/>
      <circle cx="26" cy="24" r="7.5" fill="#f2d9bd"/>
      <circle cx="74" cy="24" r="7.5" fill="#f2d9bd"/>
      <circle cx="50" cy="58" r="37" fill="#dba876" stroke="#8b5e34" stroke-width="3"/>
      <ellipse cx="50" cy="66" rx="18" ry="14" fill="#fdf1e2"/>
      <circle cx="38" cy="54" r="5.2" fill="#3e2c1c"/>
      <circle cx="62" cy="54" r="5.2" fill="#3e2c1c"/>
      <circle cx="40" cy="52" r="1.8" fill="#fff"/>
      <circle cx="64" cy="52" r="1.8" fill="#fff"/>
      <circle cx="30" cy="66" r="5.2" fill="#ffb6c1" opacity="0.65"/>
      <circle cx="70" cy="66" r="5.2" fill="#ffb6c1" opacity="0.65"/>
      <circle cx="50" cy="63" r="3.4" fill="#3e2c1c"/>
      <path d="M43 71 Q50 76 57 71" stroke="#3e2c1c" stroke-width="2.2" fill="none" stroke-linecap="round"/>
    </svg>`
  };

  function updateStageUI() {
    const startBtn = document.getElementById('start-btn');

    if (gameMode === 'free') {
      document.getElementById('story-avatar').innerText = '🎲✨🏆';
      document.getElementById('story-title').innerText = 'フリーモード：スコアアタック！';
      document.getElementById('story-desc').innerText = '好きなむずかしさを選んで、自己ベストのハイスコア更新を目指そう！';
      document.getElementById('story-target').innerText = `🏆 現在のハイスコア: ${highScore.toLocaleString()} pt`;
      document.getElementById('target-display').innerText = highScore.toLocaleString();
      startBtn.innerText = '🚀 フリーモードをはじめる！';
      setMascotMessage('rabbit', `🎲 ハイスコア ${highScore.toLocaleString()}pt を超えよう！`);
      return;
    }

    const stage = stages[currentStageIndex];
    document.getElementById('story-avatar').innerText = stage.avatar;
    document.getElementById('story-title').innerText = stage.title;
    document.getElementById('story-desc').innerText = stage.desc;
    const target = getCurrentTargetScore();
    document.getElementById('story-target').innerText = `🎯 目標スコア: ${target.toLocaleString()} pt`;
    document.getElementById('target-display').innerText = target.toLocaleString();
    startBtn.innerText = '🚀 このステージをはじめる！';
    setMascotMessage(stage.mascotKey, `🎯 ${target}pt集めよう！ ${stage.mascotStart}`);
  }

  function playCuteSE(type, comboCount = 1) {
    if (!audioCtx || !isBgmOn) return;
    try {
      const now = audioCtx.currentTime;
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();
      osc.connect(gain);
      gain.connect(audioCtx.destination);

      if (type === 'select' || type === 'click') {
        osc.type = 'sine';
        osc.frequency.setValueAtTime(600, now);
        osc.frequency.exponentialRampToValueAtTime(1200, now + 0.08);
        gain.gain.setValueAtTime(0.08, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.08);
        osc.start(now);
        osc.stop(now + 0.08);
      } else if (type === 'match') {
        osc.type = 'triangle';
        const baseFreq = 800 + (comboCount * 250);
        osc.frequency.setValueAtTime(baseFreq, now);
        osc.frequency.exponentialRampToValueAtTime(baseFreq * 1.5, now + 0.18);
        gain.gain.setValueAtTime(0.08, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.18);
        osc.start(now);
        osc.stop(now + 0.18);
      } else if (type === 'fail') {
        osc.type = 'sine';
        osc.frequency.setValueAtTime(350, now);
        osc.frequency.linearRampToValueAtTime(200, now + 0.12);
        gain.gain.setValueAtTime(0.08, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.12);
        osc.start(now);
        osc.stop(now + 0.12);
      } else if (type === 'shuffle') {
        osc.type = 'triangle';
        osc.frequency.setValueAtTime(800, now);
        osc.frequency.exponentialRampToValueAtTime(400, now + 0.18);
        gain.gain.setValueAtTime(0.07, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.18);
        osc.start(now);
        osc.stop(now + 0.18);
      }
    } catch(e) {}
  }

  function playBGMTone(freq, duration = 0.25, vol = 0.02) {
    if (!audioCtx || !isBgmOn) return;
    try {
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();
      osc.type = 'sine';
      osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
      gain.gain.setValueAtTime(vol, audioCtx.currentTime);
      gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + duration);
      osc.connect(gain);
      gain.connect(audioCtx.destination);
      osc.start();
      osc.stop(audioCtx.currentTime + duration);
    } catch(e) {}
  }

  function setBGMPhase(phase) {
    if (currentBgmPhase === phase) return;
    currentBgmPhase = phase;
    if (bgmTimer) clearInterval(bgmTimer);
    if (!isBgmOn) return;

    let step = 0;
    if (phase === 5) {
      const cuteIntro = [523.25, 659.25, 783.99, 659.25, 587.33, 659.25];
      bgmTimer = setInterval(() => {
        playBGMTone(cuteIntro[step % cuteIntro.length], 0.3, 0.02);
        step++;
      }, 480);
    } else if (phase === 1) {
      const cutePlay = [523.25, 659.25, 783.99, 659.25, 880.00, 783.99, 659.25, 587.33];
      bgmTimer = setInterval(() => {
        playBGMTone(cutePlay[step % cutePlay.length], 0.25, 0.025);
        step++;
      }, 380);
    } else if (phase === 2) {
      const cuteHurry = [659.25, 783.99, 880.00, 1046.50, 880.00, 783.99];
      bgmTimer = setInterval(() => {
        playBGMTone(cuteHurry[step % cuteHurry.length], 0.2, 0.03);
        step++;
      }, 240);
    } else if (phase === 3) {
      const resultBGM = [523.25, 659.25, 783.99, 1046.50, 880.00, 1046.50];
      bgmTimer = setInterval(() => {
        playBGMTone(resultBGM[step % resultBGM.length], 0.3, 0.03);
        step++;
      }, 300);
    }
  }

  function toggleBGM() {
    playCuteSE('click');
    isBgmOn = !isBgmOn;
    document.getElementById('bgm-btn').innerText = isBgmOn ? '🎵 BGM: ON' : '🔇 BGM: OFF';
    if (!isBgmOn && bgmTimer) {
      clearInterval(bgmTimer);
    } else if (isBgmOn) {
      initAudio();
      let p = 5;
      if (isPlaying) p = timeLeft <= 10 ? 2 : 1;
      else if (document.getElementById('result-modal').style.display === 'flex') p = 3;
      currentBgmPhase = 0;
      setBGMPhase(p);
    }
  }

  function setMascotMessage(mascotKey, text) {
    document.getElementById('mascot-avatar').innerHTML = mascotSVGs[mascotKey] || mascotSVGs.rabbit;
    document.getElementById('mascot-speech').innerText = text;
  }

  const canvas = document.getElementById('effect-canvas');
  const ctx = canvas.getContext('2d');
  let particles = [];

  function resizeCanvas() {
    const boardContainer = document.getElementById('board-container');
    canvas.width = boardContainer.clientWidth;
    canvas.height = boardContainer.clientHeight;
  }
  window.addEventListener('resize', resizeCanvas);

  function createParticles(x, y, count = 3) {
    const shapes = ['✨', '🌸', '💫', '🍎', '⭐'];
    for (let i = 0; i < count; i++) {
      const angle = Math.random() * Math.PI * 2;
      const speed = Math.random() * 2.5 + 1;
      particles.push({
        x, y,
        vx: Math.cos(angle) * speed,
        vy: Math.sin(angle) * speed,
        size: Math.random() * 5 + 9,
        shape: shapes[Math.floor(Math.random() * shapes.length)],
        alpha: 1,
        decay: 0.07
      });
    }
  }

  function updateParticles() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    if (particles.length > 0) {
      for (let i = particles.length - 1; i >= 0; i--) {
        let p = particles[i];
        p.x += p.vx;
        p.y += p.vy;
        p.alpha -= p.decay;
        ctx.save();
        ctx.globalAlpha = Math.max(0, p.alpha);
        ctx.font = `${p.size}px sans-serif`;
        ctx.fillText(p.shape, p.x, p.y);
        ctx.restore();
        if (p.alpha <= 0) particles.splice(i, 1);
      }
    }
    requestAnimationFrame(updateParticles);
  }
  requestAnimationFrame(updateParticles);

  function showFloatingText(x, y, text) {
    const el = document.createElement('div');
    el.className = 'float-text';
    el.innerText = text;
    el.style.left = `${x}px`;
    el.style.top = `${y}px`;
    document.getElementById('board-container').appendChild(el);

    el.animate([
      { opacity: 1, transform: 'translateY(0)' },
      { opacity: 0, transform: 'translateY(-28px)' }
    ], { duration: 550, easing: 'ease-out' }).onfinish = () => el.remove();
  }

  function initBoard() {
    const grid = document.getElementById('grid');
    grid.innerHTML = '';
    board = [];
    const activeTileTypes = getActiveTileTypes();

    for (let r = 0; r < ROWS; r++) {
      for (let c = 0; c < COLS; c++) {
        let validTypes = [...activeTileTypes];
        if (c >= 2) {
          const l1 = board[r * COLS + (c - 1)];
          const l2 = board[r * COLS + (c - 2)];
          if (l1 && l2 && l1.id === l2.id) validTypes = validTypes.filter(t => t.id !== l1.id);
        }
        if (r >= 2) {
          const u1 = board[(r - 1) * COLS + c];
          const u2 = board[(r - 2) * COLS + c];
          if (u1 && u2 && u1.id === u2.id) validTypes = validTypes.filter(t => t.id !== u1.id);
        }
        const chosen = validTypes[Math.floor(Math.random() * validTypes.length)];

        const el = document.createElement('div');
        el.className = `tile ${chosen.type}`;
        el.innerText = chosen.icon;
        
        grid.appendChild(el);

        const tileObj = { ...chosen, el, r, c };
        board.push(tileObj);

        attachTilePointerHandlers(el, tileObj);

        setTileTransform(tileObj);
      }
    }

    if (!findPossibleMove()) {
      shuffleBoard(false);
    }
  }

  function setTileTransform(tile) {
    const x = tile.c * TILE_SIZE + OFFSET;
    const y = tile.r * TILE_SIZE + OFFSET;
    tile.el.style.transform = `translate3d(${x}px, ${y}px, 0)`;
  }

  function renderBoardState() {
    for (let index = 0; index < board.length; index++) {
      const tile = board[index];
      tile.el.className = `tile ${tile.type}`;
      if (selectedTileIndex === index) tile.el.classList.add('selected');
      if (activeHintIndices.includes(index)) tile.el.classList.add('hint');
      tile.el.innerText = tile.icon;
      setTileTransform(tile);
    }
  }

  function shuffleBoard(userTriggered = true) {
    if (isProcessing) return;
    playCuteSE('shuffle');
    clearHint();
    selectedTileIndex = null;

    let attempts = 0;
    do {
      for (let i = board.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        const temp = board[i];
        board[i] = board[j];
        board[j] = temp;
      }
      attempts++;
    } while ((findMatches().length > 0 || !findPossibleMove()) && attempts < 100);

    for (let r = 0; r < ROWS; r++) {
      for (let c = 0; c < COLS; c++) {
        const idx = r * COLS + c;
        board[idx].r = r;
        board[idx].c = c;
      }
    }

    renderBoardState();

    if (userTriggered) {
      setMascotMessage(stages[currentStageIndex].mascotKey, '🔀 シャッフルしたよ！');
    }
  }

  async function swapTilesAnimated(idx1, idx2) {
    const t1 = board[idx1];
    const t2 = board[idx2];

    const tempR = t1.r, tempC = t1.c;
    t1.r = t2.r; t1.c = t2.c;
    t2.r = tempR; t2.c = tempC;

    board[idx1] = t2;
    board[idx2] = t1;

    const p1 = t1.el.animate([
      { transform: t1.el.style.transform },
      { transform: `translate3d(${t1.c * TILE_SIZE + OFFSET}px, ${t1.r * TILE_SIZE + OFFSET}px, 0)` }
    ], { duration: 150, easing: 'ease-out' });

    const p2 = t2.el.animate([
      { transform: t2.el.style.transform },
      { transform: `translate3d(${t2.c * TILE_SIZE + OFFSET}px, ${t2.r * TILE_SIZE + OFFSET}px, 0)` }
    ], { duration: 150, easing: 'ease-out' });

    await Promise.all([p1.finished, p2.finished]);
    setTileTransform(t1);
    setTileTransform(t2);
  }

  // 押し続けている間、指/マウスを動かした方向のタイルへスワップする操作方式
  let dragState = null; // { index, startX, startY, triggered }
  const DRAG_THRESHOLD = 18; // これ以上動かしたらスワップ方向として確定（px）

  function attachTilePointerHandlers(el, tileObj) {
    el.addEventListener('pointerdown', (e) => onTilePointerDown(e, tileObj));
  }

  function onTilePointerDown(e, tileObj) {
    if (!isPlaying || isProcessing) return;
    initAudio();
    clearHint();

    const idx = board.indexOf(tileObj);
    if (idx === -1) return;

    dragState = { index: idx, startX: e.clientX, startY: e.clientY, triggered: false };
    selectedTileIndex = idx;
    playCuteSE('select');
    renderBoardState();

    try { e.target.setPointerCapture(e.pointerId); } catch (err) {}
  }

  function onTilePointerMove(e) {
    if (!dragState || dragState.triggered) return;

    const dx = e.clientX - dragState.startX;
    const dy = e.clientY - dragState.startY;

    if (Math.abs(dx) < DRAG_THRESHOLD && Math.abs(dy) < DRAG_THRESHOLD) return;

    dragState.triggered = true;

    const originIdx = dragState.index;
    const tile = board[originIdx];
    if (!tile) { dragState = null; return; }

    let dirR = 0, dirC = 0;
    if (Math.abs(dx) > Math.abs(dy)) {
      dirC = dx > 0 ? 1 : -1;
    } else {
      dirR = dy > 0 ? 1 : -1;
    }

    const targetR = tile.r + dirR;
    const targetC = tile.c + dirC;
    dragState = null;

    if (targetR < 0 || targetR >= ROWS || targetC < 0 || targetC >= COLS) {
      selectedTileIndex = null;
      renderBoardState();
      return;
    }

    attemptSwap(originIdx, targetR * COLS + targetC);
  }

  function onTilePointerUp() {
    if (dragState && !dragState.triggered) {
      // ドラッグせずに離した場合は選択状態だけ解除
      selectedTileIndex = null;
      renderBoardState();
    }
    dragState = null;
  }

  window.addEventListener('pointermove', onTilePointerMove);
  window.addEventListener('pointerup', onTilePointerUp);
  window.addEventListener('pointercancel', onTilePointerUp);

  async function attemptSwap(idx1, idx2) {
    if (!isPlaying || isProcessing) return;
    if (idx2 < 0 || idx2 >= board.length) return;
    if (!isAdjacent(idx1, idx2)) return;

    isProcessing = true;
    selectedTileIndex = null;
    renderBoardState();

    await swapTilesAnimated(idx1, idx2);

    const matches = findMatches();

    if (matches.length > 0) {
      await processMatches(0);
      if (isPlaying && gameMode === 'story' && score >= getCurrentTargetScore()) {
        await stageClear();
      }
    } else {
      playCuteSE('fail');
      await swapTilesAnimated(idx1, idx2);
    }
    isProcessing = false;
  }

  function isAdjacent(idx1, idx2) {
    const r1 = board[idx1].r, c1 = board[idx1].c;
    const r2 = board[idx2].r, c2 = board[idx2].c;
    return (Math.abs(r1 - r2) + Math.abs(c1 - c2)) === 1;
  }

  function findMatches() {
    const matchedIndices = new Set();
    for (let r = 0; r < ROWS; r++) {
      for (let c = 0; c < COLS - 2; c++) {
        const i1 = r * COLS + c, i2 = r * COLS + (c + 1), i3 = r * COLS + (c + 2);
        if (board[i1] && board[i2] && board[i3] && 
            board[i1].id === board[i2].id && board[i2].id === board[i3].id) {
          matchedIndices.add(i1); matchedIndices.add(i2); matchedIndices.add(i3);
        }
      }
    }
    for (let c = 0; c < COLS; c++) {
      for (let r = 0; r < ROWS - 2; r++) {
        const i1 = r * COLS + c, i2 = (r + 1) * COLS + c, i3 = (r + 2) * COLS + c;
        if (board[i1] && board[i2] && board[i3] && 
            board[i1].id === board[i2].id && board[i2].id === board[i3].id) {
          matchedIndices.add(i1); matchedIndices.add(i2); matchedIndices.add(i3);
        }
      }
    }
    return Array.from(matchedIndices);
  }

  function findPossibleMove() {
    for (let i = 0; i < board.length; i++) {
      const r = board[i].r, c = board[i].c;
      const neighbors = [];
      if (c < COLS - 1) neighbors.push(i + 1);
      if (r < ROWS - 1) neighbors.push(i + COLS);

      for (let neighbor of neighbors) {
        const temp = board[i]; board[i] = board[neighbor]; board[neighbor] = temp;
        const matches = findMatches();
        const tempBack = board[i]; board[i] = board[neighbor]; board[neighbor] = tempBack;

        if (matches.length > 0) return [i, neighbor];
      }
    }
    return null;
  }

  function useAssistHint() {
    playCuteSE('click');
    if (!isPlaying || isProcessing) return;
    const move = findPossibleMove();
    if (move) {
      activeHintIndices = move;
      renderBoardState();
      setMascotMessage(stages[currentStageIndex].mascotKey, '💡 ここを動かしてみて！');
    } else {
      setMascotMessage(stages[currentStageIndex].mascotKey, '🔄 手詰まりだからシャッフルするね！');
      shuffleBoard(false);
    }
  }

  function clearHint() {
    if (activeHintIndices.length > 0) {
      activeHintIndices = [];
      renderBoardState();
    }
  }

  async function processMatches(comboCount) {
    let matches = findMatches();

    if (matches.length === 0) {
      document.getElementById('combo').innerText = comboCount;
      if (!findPossibleMove()) {
        setMascotMessage(stages[currentStageIndex].mascotKey, '✨ 自動シャッフル発動！');
        await delay(200);
        shuffleBoard(false);
      }
      return;
    }

    comboCount++;
    document.getElementById('combo').innerText = comboCount;

    const mKey = stages[currentStageIndex].mascotKey;
    if (comboCount === 1) setMascotMessage(mKey, '🌟 いい調子！どんどん消そう！');
    else if (comboCount === 2) setMascotMessage(mKey, '🔥 2連鎖！すごいね！');
    else if (comboCount >= 3) setMascotMessage(mKey, `🎉 ${comboCount}連鎖！最高に盛り上がってきた！`);

    playCuteSE('match', comboCount);

    const animPromises = matches.map(idx => {
      const tile = board[idx];
      createParticles((tile.c * TILE_SIZE) + 40, (tile.r * TILE_SIZE) + 40, 3);
      return tile.el.animate([
        { transform: `${tile.el.style.transform} scale(1)`, opacity: 1 },
        { transform: `${tile.el.style.transform} scale(0.3)`, opacity: 0 }
      ], { duration: 150, easing: 'ease-out' }).finished;
    });

    const firstIdx = matches[0];
    const fx = (board[firstIdx].c * TILE_SIZE) + 11;
    const fy = (board[firstIdx].r * TILE_SIZE) + 11;
    
    // スコア計算（95%減のバランス）
    const baseScore = matches.length * 10 * comboCount;
    const addScore = Math.max(1, Math.round(baseScore * getScoreMultiplier()));
    showFloatingText(fx, fy, `+${addScore}`);

    score += addScore;
    clearedCount += matches.length;
    if (score > highScore) highScore = score;

    document.getElementById('score').innerText = score;
    document.getElementById('cleared').innerText = clearedCount;
    document.getElementById('highscore').innerText = highScore;

    await Promise.all(animPromises);

    const grid = document.getElementById('grid');
    const activeTileTypes = getActiveTileTypes();

    for (let c = 0; c < COLS; c++) {
      let colTiles = [];
      for (let r = 0; r < ROWS; r++) {
        const idx = r * COLS + c;
        if (!matches.includes(idx)) {
          colTiles.push(board[idx]);
        } else {
          if (board[idx].el.parentNode) grid.removeChild(board[idx].el);
        }
      }
      const missing = ROWS - colTiles.length;
      for (let i = 0; i < missing; i++) {
        const rand = activeTileTypes[Math.floor(Math.random() * activeTileTypes.length)];
        const el = document.createElement('div');
        el.className = `tile ${rand.type}`;
        el.innerText = rand.icon;
        
        const tileObj = { ...rand, el, r: 0, c };
        attachTilePointerHandlers(el, tileObj);

        grid.appendChild(el);
        colTiles.unshift(tileObj);
      }
      for (let r = 0; r < ROWS; r++) {
        colTiles[r].r = r;
        colTiles[r].c = c;
        board[r * COLS + c] = colTiles[r];
        setTileTransform(colTiles[r]);
      }
    }

    await delay(100);
    await processMatches(comboCount);
  }

  function delay(ms) { return new Promise(res => setTimeout(res, ms)); }

  function startTimer() {
    clearInterval(timerInterval);
    timeLeft = GAME_TIME;
    document.getElementById('timer').innerText = timeLeft;
    document.getElementById('timer-box').classList.remove('hurry');

    setBGMPhase(1);

    timerInterval = setInterval(() => {
      timeLeft--;
      document.getElementById('timer').innerText = timeLeft;

      if (timeLeft === 10) {
        document.getElementById('timer-box').classList.add('hurry');
        setBGMPhase(2);
        setMascotMessage(stages[currentStageIndex].mascotKey, '⏰ ああと10秒！急いで〜！');
      }

      if (timeLeft <= 0) endGame();
    }, 1000);
  }

  function startGame() {
    playCuteSE('click');
    initAudio();
    resizeCanvas();
    score = 0;
    clearedCount = 0;
    selectedTileIndex = null;
    activeHintIndices = [];
    isProcessing = false;
    isPlaying = true;
    prevHighScoreForRun = highScore;

    document.getElementById('score').innerText = score;
    document.getElementById('cleared').innerText = clearedCount;
    document.getElementById('combo').innerText = 0;
    document.getElementById('target-display').innerText = getCurrentTargetScore().toLocaleString();

    initBoard();
    document.getElementById('start-overlay').style.display = 'none';
    document.getElementById('result-modal').style.display = 'none';

    if (gameMode === 'free') {
      setMascotMessage('rabbit', `🎲 ハイスコア ${highScore.toLocaleString()}pt を超えよう！`);
    } else {
      setMascotMessage(stages[currentStageIndex].mascotKey, `🎯 ${getCurrentTargetScore()}pt集めよう！ ${stages[currentStageIndex].mascotStart}`);
    }
    startTimer();
  }

  // リザルト画面の表示内容を共通で組み立てる（ストーリー／フリーモード両対応）
  function showResultModal() {
    const modal = document.getElementById('result-modal');
    modal.style.display = 'flex';

    document.getElementById('result-score').innerText = score;

    const clearStatus = document.getElementById('clear-status');
    const resultMsg = document.getElementById('result-message');
    const nextBtn = document.getElementById('next-btn');

    if (gameMode === 'free') {
      const isNewRecord = score > prevHighScoreForRun;
      clearStatus.innerText = isNewRecord ? "NEW RECORD! 🏆" : "RESULT";
      clearStatus.className = isNewRecord ? "clear-status success" : "clear-status fail";
      resultMsg.innerText = isNewRecord
        ? `🎉 ハイスコア更新！ ${score.toLocaleString()}pt を記録したよ！`
        : `今回のスコアは ${score.toLocaleString()}pt！ハイスコア ${prevHighScoreForRun.toLocaleString()}pt を目指してもう一度！`;
      nextBtn.innerText = "もう一度ちょうせんする 🔄";
      return;
    }

    const target = getCurrentTargetScore();
    const isClear = score >= target;

    if (isClear) {
      clearStatus.innerText = "STAGE CLEAR! 🎉";
      clearStatus.className = "clear-status success";

      if (currentStageIndex < stages.length - 1) {
        resultMsg.innerText = `🎯 目標 ${target.toLocaleString()}pt 達成！次のステージへ移動します！`;
        nextBtn.innerText = "つぎのステージへ進む ➔";
      } else {
        resultMsg.innerText = "🏆 すべてのステージクリア！最高の森のパーティーは大成功です！";
        nextBtn.innerText = "最初からあそぶ 🔄";
      }
    } else {
      clearStatus.innerText = "TRY AGAIN... 💦";
      clearStatus.className = "clear-status fail";
      resultMsg.innerText = `目標の ${target.toLocaleString()}pt に届かなかったよ…もう一度挑戦しよう！`;
      nextBtn.innerText = "もう一度挑戦する 🔄";
    }
  }

  async function endGame() {
    isPlaying = false;
    clearInterval(timerInterval);

    setBGMPhase(3); // クリアBGM（クリア・リザルト共通のジングル）

    await delay(500);
    showResultModal();
  }

  // 目標スコアに到達した瞬間（時間切れを待たずに）ステージクリア画面を表示（ストーリーモードのみ）
  async function stageClear() {
    isPlaying = false;
    clearInterval(timerInterval);

    setBGMPhase(3); // クリアBGM

    await delay(400);
    showResultModal();
  }

  function nextStageOrRetry() {
    playCuteSE('click');
    const target = getCurrentTargetScore();

    if (gameMode === 'story' && score >= target) {
      if (currentStageIndex < stages.length - 1) {
        currentStageIndex++;
        // ストーリーモードのクリア時は次のステージへ進んだあと、すぐに自動でゲームをスタートさせる
        resetGame();
        startGame();
        return;
      } else {
        currentStageIndex = 0;
      }
    }

    resetGame();
  }

  function resetGame() {
    playCuteSE('click');
    isPlaying = false;
    isProcessing = false;
    clearInterval(timerInterval);
    if (bgmTimer) clearInterval(bgmTimer);
    currentBgmPhase = 0;
    selectedTileIndex = null;
    activeHintIndices = [];
    dragState = null;

    score = 0;
    clearedCount = 0;
    document.getElementById('score').innerText = 0;
    document.getElementById('combo').innerText = 0;
    document.getElementById('cleared').innerText = 0;
    document.getElementById('timer').innerText = GAME_TIME;

    updateStageUI();
    
    initBoard();
    document.getElementById('start-overlay').style.display = 'flex';
    document.getElementById('result-modal').style.display = 'none';
    
    setBGMPhase(5);
  }

  initBoard();
</script>

</body>
</html>