<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>music for you</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body {
  margin: 0;
  font-family: "Century Gothic", "Segoe UI", Arial, sans-serif;
  background: linear-gradient(135deg,#fde2e4,#fff1f2,#fef3c7);
  color: #374151;
}

/* TOP SEARCH */
.topbar {
  padding: 14px 16px;
  background: rgba(255,255,255,0.6);
  backdrop-filter: blur(10px);
}
.topbar input {
  width: 100%;
  padding: 10px 16px;
  border-radius: 999px;
  border: none;
  outline: none;
  background: rgba(255,255,255,0.85);
}

/* LAYOUT */
.container {
  display: flex;
  height: calc(100vh - 64px);
}

/* SIDEBAR */
.sidebar {
  width: 260px;
  padding: 24px;
  background: rgba(255,255,255,0.45);
}
.sidebar h3 { opacity:.6; margin-bottom:16px; }
.playlist-btn {
  cursor: pointer;
  margin-bottom: 10px;
  opacity: .8;
}
.playlist-btn:hover { opacity:1; font-weight:600; }

/* MAIN */
.main {
  flex:1;
  padding:30px;
  overflow-y:auto;
}
.header {
  display:flex;
  gap:20px;
  margin-bottom:30px;
}
.header img {
  width:150px;
  height:150px;
  border-radius:14px;
}
.header h1 {
  margin:0;
  font-size:2.6rem;
}
.poetry {
  opacity:.65;
  font-style:italic;
  margin-top:6px;
}

/* SONG LIST */
.song-header, .song {
  display:grid;
  grid-template-columns:40px 60px 2fr 1.5fr 60px;
  padding:10px;
  align-items:center;
}
.song-header {
  opacity:.5;
  border-bottom:1px solid rgba(0,0,0,.08);
}
.song:hover {
  background:rgba(255,255,255,.35);
  cursor:pointer;
}

/* PLAYER */
.player {
  position:fixed;
  bottom:0;
  width:100%;
  background:rgba(255,255,255,.75);
  backdrop-filter:blur(12px);
  border-top:1px solid rgba(0,0,0,.05);
  padding:12px 16px;
  display:grid;
  grid-template-columns:1fr auto 1fr;
  align-items:center;
}
.player-left {
  display:flex;
  gap:12px;
  align-items:center;
}
.player-left img {
  width:48px;
  height:48px;
  border-radius:8px;
}
.controls {
  display:flex;
  gap:18px;
}
.controls button {
  background:none;
  border:none;
  font-size:20px;
  cursor:pointer;
}

/* PROGRESS */
.progress-wrap {
  grid-column:1/-1;
  display:flex;
  gap:10px;
  align-items:center;
  margin-top:8px;
  font-size:.75rem;
  opacity:.7;
}
.progress {
  flex:1;
  height:4px;
  background:rgba(0,0,0,.15);
  border-radius:10px;
  cursor:pointer;
}
.progress-bar {
  height:100%;
  width:0%;
  background:#ec4899;
  border-radius:10px;
}

/* 📱 MOBILE OPTIMIZATION */
@media (max-width: 768px) {

  .container {
    flex-direction: column;
  }

  .sidebar {
    width: 100%;
    display: flex;
    overflow-x: auto;
    gap: 16px;
    padding: 14px;
  }

  .playlist-btn {
    white-space: nowrap;
    font-size: .9rem;
  }

  .header {
    flex-direction: column;
    align-items: center;
    text-align: center;
  }

  .header img {
    width:120px;
    height:120px;
  }

  .header h1 {
    font-size:1.8rem;
  }

  .song-header {
    display:none;
  }

  .song {
    grid-template-columns:40px 50px 1fr;
  }

  .song span:nth-child(4),
  .song span:nth-child(5) {
    display:none;
  }

  .player {
    padding-bottom: env(safe-area-inset-bottom);
  }
}
</style>
</head>

<body>

<div class="topbar">
  <input placeholder="What do you want to play?" />
</div>

<div class="container">

  <!-- SIDEBAR -->
  <div class="sidebar">
    <div class="playlist-btn" onclick="load('morning')">🌅 morning feels</div>
    <div class="playlist-btn" onclick="load('goto')">🎧 my go to songs</div>
    <div class="playlist-btn" onclick="load('cry')">🖤 Cry with me</div>
    <div class="playlist-btn" onclick="load('emo')">🌧️ endhukoo emo</div>
    <div class="playlist-btn" onclick="load('voice')">🤍 her voice</div>
    <div class="playlist-btn" onclick="load('gand')">🔥 gand maro</div>
  </div>

  <!-- MAIN -->
  <div class="main">
    <div class="header">
      <img id="cover" src="/images/1.webp">
      <div>
        <h1 id="title">morning feels 🥰</h1>
        <div class="poetry" id="poetry">I wake up softer than yesterday</div>
      </div>
    </div>

    <div class="song-header">
      <span>#</span><span></span><span>Title</span><span>Album</span><span>⏱</span>
    </div>

    <div id="songs"></div>
  </div>
</div>

<!-- PLAYER -->
<div class="player">
  <div class="player-left">
    <img id="pimg" src="/images/1.webp">
    <div>
      <div id="ptitle">Mera Mann Kehne Laga</div>
      <div id="partist" style="opacity:.6;font-size:.85rem">Falak Shabbir</div>
    </div>
  </div>

  <div class="controls">
    <button onclick="audio.currentTime=0">⏮</button>
    <button onclick="audio.paused?audio.play():audio.pause()">⏯</button>
    <button onclick="audio.currentTime=0">⏭</button>
  </div>

  <div></div>

  <div class="progress-wrap">
    <span id="cur">0:00</span>
    <div class="progress" onclick="seek(event)">
      <div class="progress-bar" id="bar"></div>
    </div>
    <span id="dur">0:00</span>
  </div>

  <audio id="audio"></audio>
</div>

<script>
const playlists = {
  morning: {
    title: "morning feels 🥰",
    poetry: "I wake up softer than yesterday",
    songs: [
      {
        title: "Mera Mann Kehne Laga",
        artist: "Falak Shabbir",
        album: "Nautanki Saala",
        time: "3:47",
        img: "/images/1.webp",
        src: "/songs/Mera Mann Kehne Laga Nautanki Saala! 320 Kbps.mp3"
      },
      {
        title: "Tum Se Hi",
        artist: "Mohit Chauhan",
        album: "Jab We Met",
        time: "5:21",
        img: "/images/2.webp",
        src: "/songs/Tum Se Hi Jab We Met 320 Kbps.mp3"
      }
    ]
  }
};

const audio = document.getElementById("audio");
const songsDiv = document.getElementById("songs");

function load(key){
  const p = playlists[key];
  title.textContent = p.title;
  poetry.textContent = p.poetry;
  cover.src = p.songs[0]?.img || cover.src;
  songsDiv.innerHTML = "";

  p.songs.forEach((s,i)=>{
    const d = document.createElement("div");
    d.className="song";
    d.innerHTML=`
      <span>${i+1}</span>
      <img src="${s.img}" width="42">
      <span>${s.title}</span>`;
    d.onclick=()=>play(s);
    songsDiv.appendChild(d);
  });
}

function play(s){
  audio.src=s.src;
  audio.play();
  pimg.src=s.img;
  ptitle.textContent=s.title;
  partist.textContent=s.artist;
}

audio.onloadedmetadata=()=>dur.textContent=format(audio.duration);
audio.ontimeupdate=()=>{
  bar.style.width=(audio.currentTime/audio.duration)*100+"%";
  cur.textContent=format(audio.currentTime);
};

function seek(e){
  audio.currentTime=(e.offsetX/e.target.offsetWidth)*audio.duration;
}

function format(t){
  const m=Math.floor(t/60);
  const s=Math.floor(t%60).toString().padStart(2,"0");
  return `${m}:${s}`;
}

load("morning");
</script>

</body>
</html>

