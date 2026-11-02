
<!DOCTYPE html>
<html lang="kk">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>BEREKE~FRUITS</title>
<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#f5f8f4;color:#183b29}
header{background:#176b45;color:white;padding:18px;display:flex;justify-content:space-between;align-items:center;position:sticky;top:0}
.logo{font-size:23px;font-weight:900}
button{border:0;border-radius:10px;padding:11px 15px;cursor:pointer;font-weight:bold}
.green{background:#176b45;color:white}
.light{background:#e7f3e9;color:#176b45}
main{max-width:1100px;margin:auto;padding:18px}
.hero{background:#e3f1e4;border-radius:20px;padding:25px;margin-bottom:20px}
.hero h1{font-size:32px;margin:0 0 10px}
input,select{padding:12px;border:1px solid #d5e2d7;border-radius:10px;width:100%;margin:5px 0}
.categories{display:flex;gap:8px;overflow:auto;margin:15px 0}
.categories button{white-space:nowrap}
.grid{display:grid;grid-template-columns:repeat(4,1fr);gap:14px}
.card{background:white;border-radius:16px;padding:12px;box-shadow:0 3px 14px #0000000b}
.photo{height:130px;background:#e9f3e9;border-radius:12px;display:grid;place-items:center;font-size:60px}
.card h3{margin:10px 0 5px}
.price{font-size:19px;color:#176b45;font-weight:bold;margin:8px 0}
.panel{background:white;border-radius:16px;padding:18px;margin:18px 0}
.hidden{display:none}
.cartline{display:flex;justify-content:space-between;padding:12px 0;border-bottom:1px solid #eee}
footer{text-align:center;padding:30px;color:#718078}
@media(max-width:700px){.grid{grid-template-columns:repeat(2,1fr)}.hero h1{font-size:25px}}
</style>
</head>
<body>
<header>
 <div class="logo">BEREKE~FRUITS 🍏</div>
 <div>
  <button class="light" onclick="changeLang()">ҚАЗ/РУС</button>
  <button class="light" onclick="openCart()">🛒 <span id="count">0</span></button>
  <button class="light" onclick="openAdmin()">⚙️</button>
 </div>
</header>

<main>
<section class="hero">
<h1 id="hero">Табиғи дәм — күн сайын!</h1>
<p>Жаңа піскен жемістер мен көкөністер</p>
</section>

<input id="search" placeholder="Тауар іздеу..." oninput="render()">

<div class="categories" id="categories"></div>
<section class="grid" id="products"></section>

<section class="panel hidden" id="cart">
<h2>Себет</h2>
<div id="cartItems"></div>
<h2 id="total">0 ₸</h2>
<div id="promo" class="hidden">
<select id="choice" onchange="renderCart()">
<option value="free">Тегін жеткізу</option>
<option value="discount">5% жеңілдік</option>
</select>
</div>
<input id="customer" placeholder="Атыңыз">
<input id="phone" placeholder="Телефон">
<input id="address" placeholder="Жеткізу мекенжайы">
<button class="green" onclick="order()">WhatsApp арқылы тапсырыс беру</button>
</section>

<section class="panel hidden" id="admin">
<h2>Әкімшілік бөлім</h2>
<div id="login">
<input id="pin" type="password" placeholder="PIN-код">
<button class="green" onclick="login()">Кіру</button>
<p>Бастапқы PIN: 1234</p>
</div>
<div id="management" class="hidden">
<h3>Жаңа тауар қосу</h3>
<input id="newName" placeholder="Тауар атауы">
<input id="newPrice" type="number" placeholder="Бағасы">
<input id="newOrigin" placeholder="Шыққан жері">
<select id="newCategory">
<option value="Жемістер">Жемістер</option>
<option value="Көкөністер">Көкөністер</option>
<option value="Жидектер">Жидектер</option>
<option value="Салаттар">Салаттар</option>
<option value="Жаңғақтар">Жаңғақтар</option>
</select>
<select id="newUnit">
<option>кг</option><option>дана</option><option>қорап</option>
</select>
<button class="green" onclick="addProduct()">Тауар қосу</button>
<h3>WhatsApp нөмірі</h3>
<input id="wa" placeholder="77011234567">
<button class="green" onclick="saveSettings()">Сақтау</button>
<h3>Тауарлар</h3>
<div id="adminList"></div>
<button class="light" onclick="logout()">Шығу</button>
</div>
</section>
</main>
<footer>© BEREKE~FRUITS · Almaty</footer>

<script>
const defaults=[
["Қызыл алма",850,"Жемістер","Қазақстан","🍎","кг"],
["Жасыл алма",900,"Жемістер","Қазақстан","🍏","кг"],
["Банан",1100,"Жемістер","Эквадор","🍌","кг"],
["Апельсин",950,"Жемістер","Египет","🍊","кг"],
["Мандарин",1200,"Жемістер","Марокко","🍊","кг"],
["Жүзім Хусайын",1800,"Жемістер","Өзбекстан","🍇","кг"],
["Киш-миш",2200,"Жемістер","Өзбекстан","🍇","кг"],
["Алмұрт",1400,"Жемістер","Қытай","🍐","кг"],
["Анар",1600,"Жемістер","Түркия","🔴","кг"],
["Киви",1500,"Жемістер","Иран","🥝","кг"],
["Авокадо",900,"Жемістер","Перу","🥑","дана"],
["Лимон",1300,"Жемістер","Түркия","🍋","кг"],
["Ананас",2500,"Жемістер","Коста-Рика","🍍","дана"],
["Қызанақ",900,"Көкөністер","Қазақстан","🍅","кг"],
["Қияр",700,"Көкөністер","Қазақстан","🥒","кг"],
["Картоп",250,"Көкөністер","Қазақстан","🥔","кг"],
["Сәбіз",300,"Көкөністер","Қазақстан","🥕","кг"],
["Көкжидек",2500,"Жидектер","Қазақстан","🫐","қорап"],
["Құлпынай",2200,"Жидектер","Қазақстан","🍓","кг"],
["Кимчи",1800,"Салаттар","Қазақстан","🥗","кг"],
["Квашеная капуста",900,"Салаттар","Қазақстан","🥬","кг"],
["Марковчан",1200,"Салаттар","Қазақстан","🥗","кг"],
["Жаңғақ",2800,"Жаңғақтар","Қазақстан","🌰","кг"]
].map((p,i)=>({id:i+1,name:p[0],price:p[1],cat:p[2],origin:p[3],emoji:p[4],unit:p[5]}));

let items=JSON.parse(localStorage.getItem("bf_items")||"null")||defaults;
let cart=JSON.parse(localStorage.getItem("bf_cart")||"{}");
let settings=JSON.parse(localStorage.getItem("bf_settings")||'{"pin":"1234","wa":"","limit":12000}');
let category="Барлығы",language="kk";

function save(){
 localStorage.setItem("bf_items",JSON.stringify(items));
 localStorage.setItem("bf_cart",JSON.stringify(cart));
 localStorage.setItem("bf_settings",JSON.stringify(settings));
}
function money(n){return Number(n).toLocaleString("ru-RU")+" ₸"}
function renderCats(){
 let cats=["Барлығы","Жемістер","Көкөністер","Жидектер","Салаттар","Жаңғақтар"];
 document.getElementById("categories").innerHTML=cats.map(c=>
 `<button class="${category===c?'green':'light'}" onclick="category='${c}';render()">${c}</button>`).join("");
}
function render(){
 renderCats();
 let q=document.getElementById("search").value.toLowerCase();
 let list=items.filter(p=>(category==="Барлығы"||p.cat===category)&&p.name.toLowerCase().includes(q));
 document.getElementById("products").innerHTML=list.map(p=>
 `<article class="card">
 <div class="photo">${p.emoji||"🍎"}</div>
 <h3>${p.name}</h3><small>${p.origin||""}</small>
 <div class="price">${money(p.price)} / ${p.unit}</div>
 <button class="green" onclick="add(${p.id})">Себетке қосу</button>
 </article>`).join("");
}
function add(id){cart[id]=(cart[id]||0)+1;save();renderCart()}
function total(){return Object.entries(cart).reduce((s,[id,n])=>s+(items.find(p=>p.id==id)?.price||0)*n,0)}
function renderCart(){
 let entries=Object.entries(cart).filter(([id,n])=>n>0&&items.some(p=>p.id==id));
 document.getElementById("count").textContent=entries.reduce((s,[,n])=>s+n,0);
 document.getElementById("cartItems").innerHTML=entries.map(([id,n])=>{
 let p=items.find(x=>x.id==id);
 return `<div class="cartline"><span>${p.name} × ${n}</span><span>${money(p.price*n)} <button onclick="change(${id},-1)">−</button><button onclick="change(${id},1)">+</button></span></div>`;
 }).join("")||"Себет бос";
 let sum=total(),high=sum>=settings.limit;
 document.getElementById("promo").classList.toggle("hidden",!high);
 let discount=high&&document.getElementById("choice").value==="discount"?sum*.05:0;
 document.getElementById("total").textContent=money(sum-discount);
 save();
}
function change(id,n){cart[id]=(cart[id]||0)+n;if(cart[id]<=0)delete cart[id];renderCart()}
function openCart(){document.getElementById("cart").classList.remove("hidden");renderCart();document.getElementById("cart").scrollIntoView({behavior:"smooth"})}
function openAdmin(){document.getElementById("admin").classList.remove("hidden");document.getElementById("admin").scrollIntoView({behavior:"smooth"})}
function login(){
 if(document.getElementById("pin").value!==settings.pin)return alert("PIN қате");
 document.getElementById("login").classList.add("hidden");
 document.getElementById("management").classList.remove("hidden");
 document.getElementById("wa").value=settings.wa;
 adminList();
}
function addProduct(){
 let name=document.getElementById("newName").value.trim(),price=Number(document.getElementById("newPrice").value);
 if(!name||price<0||!price)return alert("Атау мен бағаны толтыр");
 items.push({id:Date.now(),name,price,cat:document.getElementById("newCategory").value,origin:document.getElementById("newOrigin").value,unit:document.getElementById("newUnit").value,emoji:"🍎"});
 save();render();adminList();document.getElementById("newName").value="";document.getElementById("newPrice").value="";
}
function adminList(){
 document.getElementById("adminList").innerHTML=items.map(p=>
 `<div class="cartline">${p.name} — ${money(p.price)} <button onclick="removeProduct(${p.id})">Өшіру</button></div>`).join("");
}
function removeProduct(id){items=items.filter(p=>p.id!==id);delete cart[id];save();render();renderCart();adminList()}
function saveSettings(){settings.wa=document.getElementById("wa").value.replace(/\D/g,"");save();alert("Сақталды")}
function logout(){document.getElementById("management").classList.add("hidden");document.getElementById("login").classList.remove("hidden");document.getElementById("pin").value=""}
function order(){
 if(!Object.keys(cart).length)return alert("Себет бос");
 let name=document.getElementById("customer").value,phone=document.getElementById("phone").value,address=document.getElementById("address").value;
 if(!name||!phone||!address)return alert("Барлық мәліметті толтыр");
 if(!settings.wa)return alert("Әкімшілікте WhatsApp нөмірін енгіз");
 let list=Object.entries(cart).map(([id,n])=>{let p=items.find(x=>x.id==id);return `${p.name} — ${n} ${p.unit} — ${money(p.price*n)}`}).join("\n");
 let msg=`BEREKE~FRUITS тапсырысы\n${list}\nЖалпы: ${money(total())}\nАты: ${name}\nТелефон: ${phone}\nМекенжай: ${address}`;
 window.open("https://wa.me/"+settings.wa+"?text="+encodeURIComponent(msg),"_blank");
}
function changeLang(){language=language==="kk"?"ru":"kk";document.getElementById("hero").textContent=language==="kk"?"Табиғи дәм — күн сайын!":"Натуральный вкус — каждый день!";render()}
render();renderCart();
</script>
</body>
</html>
