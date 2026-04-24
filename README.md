<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Arduino School PRO</title>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>

<style>
body{font-family:Arial;background:#1e2a38;color:white;text-align:center;}
input,button{padding:10px;margin:5px;border-radius:8px;border:none;}
.card{background:#2c3e50;padding:20px;margin:10px;border-radius:10px;}
iframe{width:90%;height:250px;border-radius:10px;}
</style>
</head>

<body>

<h1>Arduino School</h1>
<div id="app"></div>

<script>
// 🔥 ВСТАВЬ СВОЙ FIREBASE CONFIG
const firebaseConfig = {
apiKey: "YOUR_KEY",
authDomain: "YOUR_DOMAIN",
projectId: "YOUR_ID"
};

firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.firestore();

// ⚡ УКАЖИ СВОЙ EMAIL КАК АДМИН
const ADMIN_EMAIL = "admin@gmail.com";

// UI
function showLogin(){
app.innerHTML= <div class="card"> <h2>Вход</h2> <input id="email" placeholder="Email"><br> <input id="pass" type="password" placeholder="Пароль"><br> <button onclick="login()">Войти</button> <button onclick="register()">Регистрация</button> </div>;
}

function showCabinet(user){
let isAdmin = user.email === ADMIN_EMAIL;

app.innerHTML= <div class="card"> <h2>Кабинет</h2> <p>${user.email}</p>  <button onclick="showLessons()">Уроки</button> <button onclick="showTest()">Тест</button> <button onclick="loadScores()">Мои оценки</button>  ${isAdmin ? '<button onclick="adminPanel()">Админ панель</button>' : ''}  <button onclick="logout()">Выйти</button>  <div id="extra"></div> </div>;
}

// AUTH
function register(){
let email=emailEl().value;
let pass=passEl().value;

auth.createUserWithEmailAndPassword(email,pass)
.then(()=>alert("Ок"))
.catch(e=>alert(e.message));
}

function login(){
auth.signInWithEmailAndPassword(emailEl().value,passEl().value)
.catch(e=>alert(e.message));
}

function logout(){auth.signOut();}
function emailEl(){return document.getElementById("email");}
function passEl(){return document.getElementById("pass");}

// УРОКИ
function showLessons(){
document.getElementById("extra").innerHTML=<h3>Видео уроки</h3>  <iframe src="https://www.youtube.com/embed/nL34zDTPkcs"></iframe> <p>Arduino основы</p>  <iframe src="https://www.youtube.com/embed/k7q6n1v1y8Y"></iframe> <p>LED подключение</p>;
}

// ТЕСТ
let score=0;
function showTest(){
score=0;
extra.innerHTML=<h3>Тест</h3>  <p>Arduino это?</p> <button onclick="ans(true)">Плата</button> <button onclick="ans(false)">Игра</button>  <p>LED это?</p> <button onclick="ans(true)">Свет</button> <button onclick="ans(false)">Мотор</button>  <br><br> <button onclick="saveScore()">Сохранить</button>;
}

function ans(c){if(c)score++;}

function saveScore(){
let user=auth.currentUser;

db.collection("scores").add({
email:user.email,
score:score,
date:new Date().toLocaleString()
});

alert("Сохранено: "+score);
}

// МОИ ОЦЕНКИ
function loadScores(){
let user=auth.currentUser;

db.collection("scores")
.where("email","==",user.email)
.get()
.then(res=>{
let html="<h3>Мои оценки</h3>";
res.forEach(doc=>{
let d=doc.data();
html+=<p>${d.email}: ${d.score}</p>;
});
extra.innerHTML=html;
});
}

// 🔥 АДМИН ПАНЕЛЬ
function adminPanel(){
db.collection("scores").get()
.then(res=>{
let html="<h3>Админ панель</h3>";

res.forEach(doc=>{
let d=doc.data();
html+=<p>${d.email} — ${d.score}</p>;
});

extra.innerHTML=html;
});
}

// AUTH STATE
auth.onAuthStateChanged(user=>{
if(user) showCabinet(user);
else showLogin();
});
</script>

</body>
</html>
