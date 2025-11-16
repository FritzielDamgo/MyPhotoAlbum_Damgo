<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🎀My Photo Album🎀</title>
<link href="https://fonts.cdnfonts.com/css/country-schoolbook" rel="stylesheet">
<style>
body { margin:0; padding:0; font-family:'Country Schoolbook',sans-serif; background-color:#ffe6f0; text-align:center; }
header h1 { font-size:3em; margin-top:50px; }
header p { font-size:1.5em; margin-top:10px; }
.matcha-line { width:60%; height:5px; background-color:#7cc576; border:none; margin:20px auto 50px; }
.album-container { display:flex; flex-wrap:wrap; justify-content:center; gap:25px; padding:0 20px; }
.album-bubble { background-color:#ffb6c1; padding:20px 30px; border-radius:50px; font-size:1.2em; font-weight:bold; cursor:pointer; transition:transform 0.2s, box-shadow 0.2s; box-shadow:0 4px 6px rgba(0,0,0,0.2); }
.album-bubble:hover { transform:scale(1.1); box-shadow:0 6px 10px rgba(0,0,0,0.3); }
.photos-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(150px,1fr)); gap:15px; max-width:1000px; margin:40px auto; padding:0 20px; }
.photo-card { position:relative; cursor:pointer; }
.photo-card img { width:100%; height:150px; object-fit:cover; border-radius:15px; transition:transform 0.3s; }
.photo-card img:hover { transform:scale(1.05); }
.photo-title { position:absolute; bottom:5px; left:5px; background:rgba(255,255,255,0.7); padding:2px 5px; border-radius:5px; font-size:0.9em; }
.back-button { display:inline-block; margin-top:10px; background-color:#7cc576; color:white; padding:6px 12px; border-radius:8px; text-decoration:none; cursor:pointer; }
.back-button:hover { background-color:#6ab86a; }
.upload-btn { margin-top:20px; background-color:#ff69b4; color:white; padding:8px 15px; border-radius:8px; cursor:pointer; border:none; font-size:1em; }
.upload-btn:hover { background-color:#ff85c1; }
.hidden { display:none; }

.modal { display:none; position:fixed; z-index:100; left:0; top:0; width:100%; height:100%; overflow:auto; background-color: rgba(0,0,0,0.6); }
.modal-content { background-color:#fff; margin:10% auto; padding:20px; border-radius:15px; max-width:600px; text-align:center; position:relative; }
.modal-content img { max-width:100%; max-height:400px; border-radius:10px; }
.modal-close { position:absolute; top:10px; right:15px; font-size:24px; font-weight:bold; color:#333; cursor:pointer; }
.modal-desc { margin-top:10px; font-size:1em; }
</style>
</head>
<body>

<div id="home">
<header>
<h1>🎀My Photo Album🎀</h1>
<p>🌷Where every moment stays in place🌷</p>
<hr class="matcha-line">
</header>
<main class="album-container" id="albums"></main>
</div>

<div id="album-page" class="hidden">
<header>
<h1 id="album-title"></h1>
<div class="back-button" onclick="showHome()">← Back to Home</div>
</header>

<main class="photos-grid" id="photos-grid"></main>

<input type="file" id="photo-upload" class="hidden" accept="image/*" />
<button class="upload-btn" onclick="triggerUpload()">Upload Photo</button>
</div>

<div id="modal" class="modal">
  <div class="modal-content">
    <span class="modal-close" onclick="closeModal()">&times;</span>
    <img id="modal-img" src="" alt="">
    <div class="modal-desc" id="modal-desc"></div>
  </div>
</div>

<script>
const placeholder = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAKCAYAAACNMs+9AAAAHklEQVQYV2NkQAN/Gf4ZGRiGQAhjF3BGAMAYw8Ck1vAOVwAAAABJRU5ErkJggg==";

function generatePhotos(count) {
    return Array(count).fill({img: placeholder, desc: "Default Photo"});
}

const albums = [
    { name:"Mother Nature", key:"mother-nature", photos:generatePhotos(20) },
    { name:"Architecture Photos", key:"architecture", photos:generatePhotos(20) },
    { name:"Animals", key:"animals", photos:generatePhotos(20) },
    { name:"People or Events", key:"people-events", photos:generatePhotos(20) },
    { name:"Recommended Watch/Films", key:"recommended", photos:generatePhotos(20) }
];

const albumsContainer = document.getElementById('albums');
const albumPage = document.getElementById('album-page');
const homePage = document.getElementById('home');
const photosGrid = document.getElementById('photos-grid');
const albumTitle = document.getElementById('album-title');
const photoUpload = document.getElementById('photo-upload');
let currentAlbum = null;

const modal = document.getElementById('modal');
const modalImg = document.getElementById('modal-img');
const modalDesc = document.getElementById('modal-desc');

albums.forEach(album => {
    const div = document.createElement('div');
    div.className = 'album-bubble';
    div.innerText = album.name;
    div.onclick = () => showAlbum(album);
    albumsContainer.appendChild(div);
});

function showAlbum(album) {
    homePage.classList.add('hidden');
    albumPage.classList.remove('hidden');
    albumTitle.innerText = album.name;
    currentAlbum = album;
    renderPhotos(album.photos);
}

function renderPhotos(photos) {
    photosGrid.innerHTML = '';
    photos.forEach((p, i) => {
        const card = document.createElement('div');
        card.className = 'photo-card';
        const img = document.createElement('img');
        img.src = p.img;
        img.alt = p.desc;
        const titleDiv = document.createElement('div');
        titleDiv.className = 'photo-title';
        titleDiv.innerText = p.desc;
        card.appendChild(img);
        card.appendChild(titleDiv);
        card.onclick = () => openModal(p);
        photosGrid.appendChild(card);
    });
}

function showHome() {
    albumPage.classList.add('hidden');
    homePage.classList.remove('hidden');
}

function triggerUpload() {
    const desc = prompt("Enter photo title/description:");
    if(desc !== null) photoUpload.dataset.desc = desc;
    photoUpload.click();
}

photoUpload.addEventListener('change', (event) => {
    const file = event.target.files[0];
    if(file) {
        const reader = new FileReader();
        reader.onload = function(e) {
            currentAlbum.photos.push({img:e.target.result, desc:photoUpload.dataset.desc || "Uploaded Photo"});
            renderPhotos(currentAlbum.photos);
        };
        reader.readAsDataURL(file);
    }
    photoUpload.value = "";
    photoUpload.dataset.desc = "";
});

function openModal(photo) {
    modal.style.display = "block";
    modalImg.src = photo.img;
    modalDesc.innerText = photo.desc;
}
function closeModal() {
    modal.style.display = "none";
}

window.onclick = function(event) {
    if(event.target == modal) closeModal();
}
</script>
</body>
</html>
