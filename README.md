<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<title>Bosniakisches Kulturzentrum FEDZR Display</title>

<!-- PWA Manifest -->
<link rel="manifest" href="manifest.json">
<meta name="theme-color" content="#ffd369">

<style>
*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:"Segoe UI",Arial;
}

body{
height:100vh;
color:white;
overflow:hidden;
background-image:url("https://tse4.mm.bing.net/th/id/OIP.n0ETXnWKd7thLSAuo2fzQQHaFj?rs=1&pid=ImgDetMain&o=7&rm=3");
background-size:cover;
background-position:center;
position:relative;
}

body::before{
content:"";
position:absolute;
top:0;
left:0;
width:100%;
height:100%;
background:rgba(0,0,0,0.55);
z-index:-1;
}

.logo-corner{
position:absolute;
top:20px;
right:20px;
width:80px;
}

.header{
display:flex;
justify-content:center;
align-items:center;
padding:20px;
}

.mosque{
font-size:48px;
font-weight:700;
text-shadow:2px 2px 8px rgba(0,0,0,0.6);
}

.container{
height:85vh;
display:grid;
grid-template-columns:repeat(auto-fit,minmax(300px,1fr));
grid-auto-rows:minmax(250px,auto);
gap:25px;
padding:30px;
}

.panel{
background:rgba(255,255,255,0.1);
backdrop-filter:blur(15px);
border-radius:20px;
padding:25px;
box-shadow:0 15px 40px rgba(0,0,0,0.5);
display:flex;
flex-direction:column;
justify-content:center;
}

.clock{
font-size:72px;
text-align:center;
margin-bottom:10px;
}

.date{
text-align:center;
font-size:20px;
margin-bottom:5px;
}

.title{
text-align:center;
font-size:28px;
margin-bottom:15px;
font-weight:600;
}

.prayer{
display:flex;
justify-content:space-between;
font-size:22px;
margin:6px 0;
padding:8px 12px;
background:rgba(255,255,255,0.15);
border-radius:8px;
}

.ramadan{
margin-top:15px;
padding-top:10px;
border-top:1px solid rgba(255,255,255,0.3);
font-size:20px;
}

.prayer.current{
background:rgba(0,0,0,0.45);
font-weight:600;
border:2px solid rgba(255,255,255,0.3);
}

.nextPrayer{
text-align:center;
font-size:32px;
font-weight:600;
margin-bottom:10px;
}

.countdown{
text-align:center;
font-size:36px;
}

.hadith{
font-size:22px;
text-align:center;
line-height:1.6;
font-style:italic;
}

.hadith-source{
font-size:16px;
margin-top:10px;
opacity:0.85;
}

.info{
font-size:20px;
text-align:center;
line-height:1.6;
}

.photo{
width:70%;
height:150px;
object-fit:cover;
border-radius:15px;
margin-top:10px;
}
</style>
</head>

<body>

<img src="https://fedzr.de/wp-content/uploads/2021/04/cropped-logo_light.png" class="logo-corner">

<div class="header">
<div class="mosque">Bosniakisches Kulturzentrum FEDZR</div>
</div>

<div class="container">

<!-- Uhr -->
<div class="panel">
<div class="clock" id="clock"></div>
<div class="date" id="date"></div>
<div class="date" id="hijri"></div>
</div>

<!-- Gebetszeiten -->
<div class="panel">
<div class="title">Gebetszeiten</div>
<div class="prayer"><span>Fajr</span><span id="fajr"></span></div>
<div class="prayer"><span>Sonnenaufgang</span><span id="sunrise"></span></div>
<div class="prayer"><span>Dhuhr</span><span id="dhuhr"></span></div>
<div class="prayer"><span>Asr</span><span id="asr"></span></div>
<div class="prayer"><span>Maghrib</span><span id="maghrib"></span></div>
<div class="prayer"><span>Isha</span><span id="isha"></span></div>

<div class="ramadan">
<div class="prayer"><span>Sahur Ende</span><span id="sahur"></span></div>
<div class="prayer"><span>Iftar</span><span id="iftar"></span></div>
</div>
</div>

<!-- Nächstes Gebet -->
<div class="panel">
<div class="title">Nächstes Gebet</div>
<div class="nextPrayer" id="nextPrayer"></div>
<div class="countdown" id="countdown"></div>
</div>

<!-- Hadith -->
<div class="panel">
<div class="title">Hadith des Tages</div>
<div class="hadith" id="hadith"></div>
<div class="hadith-source" id="hadithSource"></div>
</div>

<!-- Info -->
<div class="panel">
<div class="title">Information</div>
<div class="info">
Freitag Khutba: 12:30<br><br>
Moschee BKZ FEDZR<br>
Drygalski Allee 33D<br><br>
Imam: Ef. Izet Bibic
<img src="https://fedzr.de/wp-content/uploads/2021/02/Izet-Bibic.jpg" class="photo">
</div>
</div>

</div>

<script>
/* Uhr */
function clock(){
let now=new Date();
document.getElementById("clock").innerHTML=
now.toLocaleTimeString("de-DE",{hour:'2-digit',minute:'2-digit',second:'2-digit'});
document.getElementById("date").innerHTML=
now.toLocaleDateString("de-DE");
}
setInterval(clock,1000);
clock();

/* Gebetszeiten */
let prayers={};
async function loadPrayers(){
let res=await fetch("https://api.aladhan.com/v1/timingsByCity?city=Munich&country=Germany&method=13");
let data=await res.json();
prayers=data.data.timings;

document.getElementById("fajr").innerText=prayers.Fajr;
document.getElementById("sunrise").innerText=prayers.Sunrise;
document.getElementById("dhuhr").innerText=prayers.Dhuhr;
document.getElementById("asr").innerText=prayers.Asr;
document.getElementById("maghrib").innerText=prayers.Maghrib;
document.getElementById("isha").innerText=prayers.Isha;

/* Sahur/Iftar */
document.getElementById("sahur").innerText=prayers.Fajr;
document.getElementById("iftar").innerText=prayers.Maghrib;

/* Hijri Datum */
document.getElementById("hijri").innerText=
data.data.date.hijri.day+" "+
data.data.date.hijri.month.en+" "+
data.data.date.hijri.year;

nextPrayer();
highlightCurrentPrayer();
setInterval(nextPrayer,1000);
setInterval(highlightCurrentPrayer,1000);
}
loadPrayers();

/* Nächstes Gebet */
function nextPrayer(){
if(Object.keys(prayers).length===0) return;
let now=new Date();
let list=[
["Fajr",prayers.Fajr],
["Dhuhr",prayers.Dhuhr],
["Asr",prayers.Asr],
["Maghrib",prayers.Maghrib],
["Isha",prayers.Isha]
];
let nextName="";
let nextTime=null;
for(let p of list){
let t=p[1].split(":");
let prayerTime=new Date();
prayerTime.setHours(parseInt(t[0]),parseInt(t[1]),0,0);
if(prayerTime>now){
nextName=p[0];
nextTime=prayerTime;
break;
}
}
if(!nextTime){
let t=prayers.Fajr.split(":");
nextTime=new Date();
nextTime.setDate(nextTime.getDate()+1);
nextTime.setHours(parseInt(t[0]),parseInt(t[1]),0,0);
nextName="Fajr";
}
document.getElementById("nextPrayer").innerText=nextName;
let diff=nextTime-now;
let h=Math.floor(diff/3600000);
let m=Math.floor((diff%3600000)/60000);
let s=Math.floor((diff%60000)/1000);
document.getElementById("countdown").innerText=h+"h "+m+"m "+s+"s";
}

/* Aktuelles Gebet hervorheben */
function highlightCurrentPrayer(){
if(Object.keys(prayers).length===0) return;
const prayerIds=["fajr","dhuhr","asr","maghrib","isha"];
const prayerTimes=["Fajr","Dhuhr","Asr","Maghrib","Isha"];
let now=new Date();
let currentIndex=-1;
for(let i=0;i<prayerTimes.length;i++){
let t=prayers[prayerTimes[i]].split(":");
let start=new Date();
start.setHours(parseInt(t[0]),parseInt(t[1]),0,0);
let end=new Date();
if(i<prayerTimes.length-1){
let next=prayers[prayerTimes[i+1]].split(":");
end.setHours(parseInt(next[0]),parseInt(next[1]),0,0);
}else{
let next=prayers["Fajr"].split(":");
end.setDate(end.getDate()+1);
end.setHours(parseInt(next[0]),parseInt(next[1]),0,0);
}
if(now>=start && now<end){
currentIndex=i;
break;
}
}
for(let i=0;i<prayerIds.length;i++){
let elem=document.getElementById(prayerIds[i]);
if(i===currentIndex){
elem.parentElement.classList.add("current");
}else{
elem.parentElement.classList.remove("current");
}
}

/* Sahur/Iftar optional highlight */
document.getElementById("sahur").parentElement.classList.add("current");
document.getElementById("iftar").parentElement.classList.add("current");
}

/* Hadithe */
const hadiths=[
{t:"Die Taten werden nach den Absichten beurteilt.",s:"Sahih al-Bukhari 1"},
{t:"Die besten Menschen sind diejenigen mit dem besten Charakter.",s:"Sahih al-Bukhari 3559"},
{t:"Ein Lächeln ist eine Sadaqa.",s:"Jami at-Tirmidhi 1956"},
{t:"Wer an Allah und den Jüngsten Tag glaubt soll Gutes sprechen oder schweigen.",s:"Sahih Muslim 6018"},
{t:"Der Starke ist nicht derjenige der andere besiegt sondern der der seinen Zorn beherrscht.",s:"Sahih al-Bukhari 6114"},
{t:"Reinheit ist die Hälfte des Glaubens.",s:"Sahih Muslim 223"},
{t:"Allah ist barmherzig zu denen die barmherzig sind.",s:"Sahih al-Bukhari 7376"},
{t:"Der Gläubige beleidigt nicht und flucht nicht.",s:"Jami at-Tirmidhi 1977"},
{t:"Der beste unter euch ist der der den Quran lernt und lehrt.",s:"Sahih al-Bukhari 5027"},
{t:"Der Muslim ist derjenige vor dessen Zunge und Hand die Menschen sicher sind.",s:"Sahih al-Bukhari 10"}
];
let todayIndex=new Date().getDate()%hadiths.length;
document.getElementById("hadith").innerText=hadiths[todayIndex].t;
document.getElementById("hadithSource").innerText="Quelle: "+hadiths[todayIndex].s;
</script>

</body>
</html>
