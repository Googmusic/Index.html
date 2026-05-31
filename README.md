# index.html
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Gig Player</title>

<link rel="manifest" href="manifest.json">
<meta name="theme-color" content="#000000">

<style>
body { margin:0; background:black; color:white; font-family:Arial; }

#topbar {
  position:fixed; top:0; width:100%;
  background:#111; padding:10px;
  display:flex; justify-content:center; flex-wrap:wrap;
}

button {
  font-size:24px; margin:5px; padding:10px 15px;
  border:none; border-radius:10px;
}

#song {
  margin-top:140px;
  padding:15px;
  font-size:32px;
  line-height:1.6;
}

.chord { color:#00ffcc; font-weight:bold; }
.section { color:#ffaa00; }
.highlight { background:rgba(255,255,255,0.2); }
</style>
</head>

<body>

<div id="topbar">
  <button onclick="prevSong()">⏮</button>
  <button onclick="nextSong()">⏭</button>
  <button onclick="toggleScroll()">▶</button>
  <button onclick="stopScroll()">⏹</button>
</div>

<div id="song"></div>

<script>
let songs = [
{
name:"Demo Song",
content:`[Verse]
G   D   Em   C
This is your gig app
G   D   Em   C
Ready for live use

[Chorus]
C   G   D   Em
Play your songs with ease`
}
];

let current = 0;
let scroll;

function displaySong(){
  let text = songs[current].content;
  let html = "";

  text.split("\n").forEach(line=>{
    if(line.startsWith("[")) html += `<div class="section">${line}</div>`;
    else if(line.match(/^[A-G]/)) html += `<div class="chord">${line}</div>`;
    else html += `<div>${line}</div>`;
  });

  document.getElementById("song").innerHTML = html;
}

function nextSong(){
  if(current < songs.length-1){ current++; displaySong(); save(); }
}

function prevSong(){
  if(current > 0){ current--; displaySong(); save(); }
}

function toggleScroll(){
  scroll = setInterval(()=> window.scrollBy(0,2), 50);
}

function stopScroll(){
  clearInterval(scroll);
}

function save(){
  localStorage.setItem("songs", JSON.stringify(songs));
  localStorage.setItem("current", current);
}

function load(){
  const s = localStorage.getItem("songs");
  if(s){
    songs = JSON.parse(s);
    current = parseInt(localStorage.getItem("current"));
  }
  displaySong();
}

// foot pedal support
document.addEventListener("keydown", e=>{
  if(e.key==="ArrowRight") nextSong();
  if(e.key==="ArrowLeft") prevSong();
  if(e.key===" ") toggleScroll();
});

// service worker
if('serviceWorker' in navigator){
  navigator.serviceWorker.register('sw.js');
}

window.onload = load;
</script>

</body>
</html>
