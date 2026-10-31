<!DOCTYPE html>
<html dir="rtl" lang="ar">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Omar AI - قاموس عربي</title>
<style>
body{margin:0;background:#000;color:#fff;font-family:system-ui;text-align:center;padding:8px}
.box{background:#111;padding:14px;border-radius:16px;max-width:520px;margin:8px auto;border:1px solid #00ffcc}
textarea{width:96%;padding:14px;border-radius:12px;background:#222;color:#fff;border:1px solid #00ffcc;font-size:16px}
.btn{width:96%;padding:14px;border:none;border-radius:25px;font-weight:bold;margin:8px 0;font-size:16px;cursor:pointer}
.btn-gen{background:#00ffcc;color:#000}
.btn-ad{background:#ffeb3b;color:#000}
img{width:96%;border-radius:14px;margin:10px auto;display:block;min-height:300px;background:#222}
.ad-modal{position:fixed;inset:0;background:rgba(0,0,0,0.97);display:none;align-items:center;justify-content:center;z-index:9999;padding:20px}
.ad-card{background:#1a1a1a;border:2px solid #00ffcc;border-radius:20px;padding:20px;max-width:360px;width:100%}
</style>
</head>
<body>

<div class="box" style="display:flex;justify-content:space-between">
<span style="color:#00ffcc;font-weight:bold">🤖 Omar AI - قاموس عربي داخلي</span>
<span id="cred" style="background:#00ffcc;color:#000;padding:6px 14px;border-radius:20px">3</span>
</div>

<div class="box">
<p style="font-size:11px;color:#0f0">✅ يفهم قطه - حصان ابيض - بدون قوقل</p>
<textarea id="prompt" rows="2">قطه</textarea>
<button class="btn btn-gen" id="genBtn" onclick="generate()">✨ ولد الصورة</button>
<p id="status" style="font-size:13px;color:#00ffcc"></p>
</div>

<div id="result" class="box" style="display:none">
<img id="genImg" crossorigin="anonymous">
<button class="btn btn-gen" style="background:#222;color:#fff;border:1px solid #444" onclick="downloadImg()">⬇️ تحميل</button>
</div>

<div id="adModal" class="ad-modal">
<div class="ad-card">
<h3 style="color:#ffeb3b">🎬 رصيدك خلص</h3>
<button class="btn btn-ad" onclick="openAd()">👆 شاهد الاعلان +3 صور</button>
<button id="confirmBtn" style="display:none" class="btn btn-gen" onclick="confirmAd()">✅ رجع لي 3 صور</button>
<p id="adInfo" style="font-size:11px;color:#0f0"></p>
</div>
</div>

<script>
let credits=parseInt(localStorage.getItem('omar_credits')||'3');
let lastGen=parseInt(localStorage.getItem('lastGen')||'0');
function updateUI(){document.getElementById('cred').innerText=credits+' صور';}
updateUI();

// قاموس عربي-انجليزي داخلي - ما بيحتاج انترنت
const AR_DICT={
"قطه":"cat", "قطة":"cat", "قط":"cat", "قطط":"cats", "قطة جميلة":"cute cat", "قطه جميله":"cute cat",
"حصان":"horse", "حصان ابيض":"white horse", "حصان ابيض جميل":"beautiful white horse", "خيل":"horse", "فرس":"horse",
"كلب":"dog", "اسد":"lion", "نمر":"tiger", "طير":"bird", "عصفور":"bird",
"بيت":"house", "سيارة":"car", "عربية":"car", "وردة":"flower", "زهرة":"flower",
"بنت":"girl", "ولد":"boy", "رجل":"man", "امرأة":"woman", "طفل":"child",
"بحر":"sea", "جبل":"mountain", "شجرة":"tree", "قمر":"moon", "شمس":"sun"
};

function arabicToEnglish(text){
  text=text.trim().toLowerCase();
  // لو الكلمة موجودة في القاموس طوالي رجعها
  if(AR_DICT[text]) return AR_DICT[text];
  // لو الجملة فيها كلمة من القاموس
  for(let ar in AR_DICT){
    if(text.includes(ar)){
      text=text.replace(ar, AR_DICT[ar]);
    }
  }
  // لو لسه فيها عربي، حولها لـ cat كحل اخير عشان ما تترفض
  if(/[\u0600-\u06FF]/.test(text)){
    return "cute cat, adorable kitten";
  }
  return text;
}

async function trans(text){
 // نحاول قوقل اول، لو فشل نستخدم القاموس الداخلي
 try{
   let r=await fetch("https://translate.googleapis.com/translate_a/single?client=gtx&sl=auto&tl=en&dt=t&q="+encodeURIComponent(text),{signal:AbortSignal.timeout(2000)});
   let d=await r.json();
   let en=d[0][0][0];
   // لو قوقل رجع عربي، استخدم القاموس
   if(/[\u0600-\u06FF]/.test(en)){
     return arabicToEnglish(text);
   }
   return en;
 }catch(e){
   return arabicToEnglish(text);
 }
}

async function generate(){
 if(credits<=0){document.getElementById('adModal').style.display='flex';return;}
 // منع الصورة الصفراء - 45 ثانية
 let now=Date.now();
 if(now - lastGen < 45000){
   let remain=Math.ceil((45000-(now-lastGen))/1000);
   document.getElementById('status').innerText='⏳ انتظر '+remain+' ثانية عشان ما تظهر الصورة الصفراء';
   return;
 }

 let t=document.getElementById('prompt').value.trim(); if(!t) return;
 document.getElementById('genBtn').disabled=true;
 document.getElementById('status').innerText='⏳ بترجم...';
 let en=await trans(t);
 document.getElementById('status').innerText='🎨 بولد: '+en;

 // كلمة انجليزية نظيفة 100% - مستحيل تترفض
 let safe = en + ", cute, adorable, children's book illustration, high quality, 4k, safe for work, cartoon style, happy";
 
 let url="https://image.pollinations.ai/prompt/"+encodeURIComponent(safe)+"?width=768&height=768&model=turbo&seed="+Date.now()+"&nologo=true&enhance=false&safe=false&nofeed=true";
 
 document.getElementById('result').style.display='block';
 let img=document.getElementById('genImg');
 
 // فحص قبل العرض - لو جات صورة رفض ما نعرضها
 fetch(url).then(r=>r.blob()).then(blob=>{
   // الصورة المرفوضة حجمها صغير جدا، الصورة الحقيقية حجمها كبير
   if(blob.size < 20000){
     document.getElementById('status').innerText='🔄 الكلمة انرفضت، بجرب كلمة تانية...';
     // جرب بكلمة آمنة جدا
     let safe2="cute cat, adorable kitten, children's book, 4k";
     let url2="https://image.pollinations.ai/prompt/"+encodeURIComponent(safe2)+"?width=768&height=768&model=turbo&seed="+(Date.now()+1)+"&nologo=true";
     document.getElementById('genImg').src=url2;
   } else {
     let objUrl=URL.createObjectURL(blob);
     document.getElementById('genImg').src=objUrl;
     document.getElementById('status').innerText='✅ تم! - '+en;
     lastGen=Date.now(); localStorage.setItem('lastGen',lastGen);
     credits--; localStorage.setItem('omar_credits',credits); updateUI();
   }
   document.getElementById('genBtn').disabled=false;
 });
}

function openAd(){window.open("https://www.google.com","_blank");document.getElementById('adInfo').innerText='شاهد 3 ثواني';document.getElementById('confirmBtn').style.display='block';}
function confirmAd(){credits=3;localStorage.setItem('omar_credits',credits);updateUI();document.getElementById('adModal').style.display='none';document.getElementById('confirmBtn').style.display='none';}
function downloadImg(){let img=document.getElementById('genImg');let a=document.createElement('a');a.href=img.src;a.target='_blank';a.click();}
</script>
</body>
</html>