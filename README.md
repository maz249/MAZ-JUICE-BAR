<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Maz Juice</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>

  <!-- شعار -->
  <header>
    <img id="logo" src="uploads/logo.png" alt="Maz Juice Logo">
    <h2 id="storeName">Maz Juice</h2>
    <p id="storeDesc">أشهى العصائر الطازجة والمنعشة 🍹</p>
  </header>

  <nav>
    <button onclick="showPage('home')">الرئيسية</button>
    <button onclick="showPage('categories')">التصنيفات</button>
    <button onclick="showPage('top')">أفضل العصائر</button>
    <button onclick="showPage('search')">البحث</button>
    <button onclick="showPage('auth')">تسجيل</button>
    <button onclick="showPage('adminLogin')">لوحة الأدمن</button>
  </nav>

  <!-- الرئيسية -->
  <div id="home" class="page active">
    <h2>مرحباً بكم في <span id="storeNameHome">Maz Juice</span> 🍹</h2>
    <p>سجّل الدخول لتستمتع بالتعليق والإعجاب والتقييم.</p>
  </div>

  <!-- التصنيفات -->
  <div id="categories" class="page">
    <h2>التصنيفات</h2>
    <div class="categories" id="categoriesList"></div>
  </div>

  <!-- أفضل العصائر -->
  <div id="top" class="page">
    <h2>🍹 أفضل العصائر حسب التقييم</h2>
    <div class="categories" id="topList"></div>
  </div>

  <!-- البحث -->
  <div id="search" class="page">
    <h2>🔎 البحث عن عصير</h2>
    <input type="text" id="searchInput" placeholder="اكتب اسم العصير..." onkeyup="searchJuices()">
    <div class="categories" id="searchResults"></div>
  </div>

  <!-- تسجيل -->
  <div id="auth" class="page">
    <h2>تسجيل الدخول / حساب جديد</h2>
    <div class="auth-form">
      <input type="text" id="username" placeholder="اسم المستخدم">
      <button onclick="register()">تسجيل / دخول</button>
    </div>
  </div>

  <!-- تسجيل دخول الأدمن -->
  <div id="adminLogin" class="page">
    <h2>🔑 تسجيل دخول الأدمن</h2>
    <div class="auth-form">
      <input type="password" id="adminPass" placeholder="كلمة مرور الأدمن">
      <button onclick="loginAdmin()">دخول الأدمن</button>
    </div>
  </div>

  <!-- لوحة الأدمن -->
  <div id="admin" class="page">
    <h2>⚙️ لوحة التحكم</h2>
    <div class="admin-form">
      <label>اسم المحل:</label>
      <input type="text" id="storeNameInput" placeholder="اسم المحل">
      <label>وصف المحل:</label>
      <input type="text" id="storeDescInput" placeholder="الوصف">

      <label>رفع شعار جديد:</label>
      <input type="file" id="logoFile" accept="image/*">

      <label>رفع خلفية جديدة:</label>
      <input type="file" id="bgFile" accept="image/*">

      <label>إضافة عصير جديد:</label>
      <input type="text" id="newJuice" placeholder="اسم العصير">
      <input type="text" id="newJuiceDesc" placeholder="وصف قصير">
      <input type="text" id="newJuicePrice" placeholder="السعر">
      <label>رفع صورة العصير:</label>
      <input type="file" id="juiceFile" accept="image/*">

      <button onclick="updateAdmin()">حفظ التغييرات</button>
    </div>
  </div>

  <script src="script.js"></script>
</body>
</html>
.details-img {
  width: 250px;
  max-width: 100%;
  border-radius: 16px;
  margin: 15px auto;
  display: block;
  box-shadow: 0 4px 12px rgba(0,0,0,0.2);
}

#commentsList {
  margin-top: 10px;
  background: #fff;
  padding: 10px;
  border-radius: 12px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}
.comment {
  border-bottom: 1px solid #eee;
  padding: 5px 0;
}
textarea {
  width: 100%;
  min-height: 60px;
  margin-top: 10px;
  border-radius: 8px;
  border: 1px solid #ccc;
  padding: 8px;
}
let comments = JSON.parse(localStorage.getItem("comments")) || {};
let currentJuice = null;

// عند الضغط على أي منتج
function showDetails(name){
  const juice = juices.find(j => j.name === name);
  if(!juice) return;

  currentJuice = name;

  document.getElementById("detailsName").innerText = juice.name;
  document.getElementById("detailsImg").src = juice.img || "";
  document.getElementById("detailsDesc").innerText = juice.desc || "";
  document.getElementById("detailsPrice").innerText = juice.price || "";
  document.getElementById("detailsIngredients").innerHTML = juice.ingredients ? `<p><b>المكونات:</b> ${juice.ingredients}</p>` : "";

  // زر الإعجاب
  document.getElementById("detailsLike").innerText = `❤️ (${likes[juice.name]||0})`;
  document.getElementById("detailsLike").onclick = ()=>like(juice.name,true);

  // التقييم بالنجوم
  const avg=getAvg(juice.name);
  document.getElementById("detailsStars").innerHTML=[1,2,3,4,5].map(n=>`<span class="star ${avg>=n?'filled':''}" onclick="rate('${juice.name}',${n},true)">★</span>`).join('');
  document.getElementById("detailsRating").innerText=`متوسط: ${avg.toFixed(1)} / 5`;

  // التعليقات
  renderComments();

  showPage("productDetails");
}

// عرض التعليقات
function renderComments(){
  const list=document.getElementById("commentsList");
  list.innerHTML="";
  if(!comments[currentJuice]) comments[currentJuice]=[];
  comments[currentJuice].forEach(c=>{
    const div=document.createElement("div");
    div.className="comment";
    div.innerText=`${c.user}: ${c.text}`;
    list.appendChild(div);
  });
}

// إضافة تعليق
function addComment(){
  if(!currentUser){alert("سجل الدخول أولا");return;}
  const text=document.getElementById("commentInput").value;
  if(!text) return;
  if(!comments[currentJuice]) comments[currentJuice]=[];
  comments[currentJuice].push({user:currentUser,text});
  localStorage.setItem("comments",JSON.stringify(comments));
  document.getElementById("commentInput").value="";
  renderComments();
}

// تعديل إعجاب وتقييم بحيث يحدث أيضًا في صفحة التفاصيل
function like(j,fromDetails=false){
  if(!currentUser){alert("سجل الدخول");return;}
  likes[j]=(likes[j]||0)+1;
  localStorage.setItem("likes",JSON.stringify(likes));
  if(fromDetails) showDetails(j); else renderCategories();
}
function rate(j,s,fromDetails=false){
  if(!currentUser){alert("سجل الدخول");return;}
  if(!ratings[j]) ratings[j]=[];
  ratings[j].push(s);
  localStorage.setItem("ratings",JSON.stringify(ratings));
  if(fromDetails) showDetails(j); else renderCategories();
}
