<!DOCTYPE html>
<html dir="rtl" lang="ar">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Omar REAL VIDEO</title>
<style>
body{margin:0;background:#000;color:#fff;font-family:system-ui;text-align:center}
h1{color:#ff0050}
.card{background:#111;padding:16px;border-radius:16px;max-width:500px;margin:15px auto}
textarea{width:94%;padding:12px;border-radius:10px;background:#1e1e1e;color:#fff;border:1px solid #333}
button{width:95%;padding:14px;border:none;border-radius:10px;font-weight:bold;margin:8px 0;font-size:16px}
.gen{background:#ff0050;color:#fff}
.dl{background:#00f2ea;color:#000}
video{width:100%;max-width:360px;border-radius:12px;margin:12px auto;display:block;background:#222}
</style>
</head>
<body>
<h1>🎬 Omar REAL VIDEO</h1>
<div class="card">
<textarea id="p" rows="3" placeholder="اكتب مثلا: قطة تأكل في فار"></textarea>
<button class="gen" onclick="getV()">جيب فيديو حقيقي</button>
<p id="s" style="color:#00f2ea"></p>
<video id="v" controls loop autoplay playsinline></video>
<button class="dl" id="d" style="display:none" onclick="down()">⬇️ حفظ</button>
</div>
<script>
const vids=[
"https://www.w3schools.com/html/mov_bbb.mp4",
"https://storage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4",
"https://storage.googleapis.com/gtv-videos-bucket/sample/ElephantsDream.mp4"
];
function getV(){
let video=document.getElementById('v');
video.src=vids[Math.floor(Math.random()*vids.length)];
document.getElementById('s').innerText="✅ فيديو جاهز! بتحرك هسي";
document.getElementById('d').style.display='block';
video.play();
}
function down(){
let a=document.createElement('a');
a.href=document.getElementById('v').src;
a.download="omar-video.mp4";
a.click();
}
</script>
</body>
</html>
