# Shri-ram
Building materials 
<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>श्री राम बिल्डिंग मटेरियल</title>

<style>
body {
  margin:0;
  font-family:'Segoe UI', sans-serif;
  background:#f1f3f6;
}

/* HEADER */
header {
  background:#1b5e20;
  color:white;
  padding:15px;
  text-align:center;
  position:sticky;
  top:0;
}

#ownerPhoto {
  width:50px;
  height:50px;
  border-radius:50%;
  position:absolute;
  right:10px;
  top:10px;
  object-fit:cover;
  cursor:pointer;
}

/* MENU */
.menu {
  position:absolute;
  left:10px;
  top:10px;
  font-size:22px;
  cursor:pointer;
}

.dropdown {
  display:none;
  position:absolute;
  left:10px;
  top:40px;
  background:white;
  border-radius:8px;
  box-shadow:0 2px 5px rgba(0,0,0,0.3);
}
.dropdown div {
  padding:10px;
  cursor:pointer;
}
.dropdown div:hover { background:#eee; }

/* INPUT */
#custName, #searchBar {
  width:90%;
  margin:10px auto;
  padding:12px;
  border-radius:25px;
  border:none;
  display:block;
  box-shadow:0 2px 5px rgba(0,0,0,0.2);
}

/* PRODUCT GRID */
#products {
  display:grid;
  grid-template-columns:repeat(auto-fill,minmax(160px,1fr));
  gap:10px;
  padding:10px;
}

.product {
  background:white;
  border-radius:12px;
  padding:10px;
  box-shadow:0 2px 6px rgba(0,0,0,0.15);
}

.product img {
  width:100%;
  height:100px;
  object-fit:cover;
  border-radius:8px;
}

.product h3 {
  font-size:14px;
  margin:5px 0;
}

.price {
  color:#1b5e20;
  font-weight:bold;
}

.product input {
  width:100%;
  padding:6px;
  margin-top:5px;
}

button {
  width:100%;
  padding:8px;
  border:none;
  border-radius:8px;
  margin-top:5px;
  cursor:pointer;
}

.addBtn {
  background:#1b5e20;
  color:white;
}

.deleteBtn {
  background:red;
  color:white;
}

/* CART */
#cartBox {
  position:fixed;
  bottom:0;
  width:100%;
  background:white;
  padding:10px;
  box-shadow:0 -2px 10px rgba(0,0,0,0.2);
}

#total {
  text-align:center;
  font-weight:bold;
}

.whatsapp {
  background:#25D366;
  color:white;
}
</style>
</head>

<body>

<header>
<span class="menu" onclick="toggleMenu()">☰</span>
श्री राम बिल्डिंग मटेरियल

<div class="dropdown" id="menuBox">
  <div onclick="login()">Admin Login</div>
  <div onclick="addProduct()">Product Add</div>
</div>

<img id="ownerPhoto" onclick="uploadPhoto()">
</header>

<input id="custName" placeholder="अपना नाम लिखें">
<input id="searchBar" placeholder="🔍 खोजें..." oninput="search()">

<div id="products"></div>

<div id="cartBox">
<div id="cart"></div>
<div id="total">₹0</div>
<button class="whatsapp" onclick="sendWhats()">WhatsApp Order</button>
</div>

<input type="file" id="fileInput" style="display:none" accept="image/*">

<script>
let admin=false;
let password="1234";

let products = JSON.parse(localStorage.getItem("products")) || [];
let cart=[];

/* MENU */
function toggleMenu(){
 let m=document.getElementById("menuBox");
 m.style.display=m.style.display=="block"?"none":"block";
}

/* LOGIN */
function login(){
 let p=prompt("Password");
 if(p==password){
  admin=true;
  alert("Edit Mode ON");
  render();
 }
}

/* OWNER PHOTO */
function uploadPhoto(){
 if(!admin) return alert("Login first");

 document.getElementById("fileInput").click();
}

fileInput.onchange = function(e){
 let file = e.target.files[0];
 let reader = new FileReader();

 reader.onload = function(){
  ownerPhoto.src = reader.result;
  localStorage.setItem("ownerPhoto", reader.result);
 };

 reader.readAsDataURL(file);
};

/* ADD PRODUCT */
function addProduct(){
 if(!admin) return alert("Login first");

 let name = prompt("नाम");
 let price = prompt("रेट");

 if(!name || !price) return;

 let input = document.createElement("input");
 input.type="file";
 input.accept="image/*";

 input.onchange = function(e){
  let file = e.target.files[0];
  let reader = new FileReader();

  reader.onload = function(){
    products.push({
      name,
      price:parseFloat(price),
      img:reader.result
    });
    save();
    render();
  };

  reader.readAsDataURL(file);
 };

 input.click();
}

/* SAVE */
function save(){
 localStorage.setItem("products", JSON.stringify(products));
}

/* RENDER */
function render(){
 let box=document.getElementById("products");
 box.innerHTML="";

 products.forEach((p,i)=>{
  box.innerHTML+=`
  <div class="product">
    <img src="${p.img}">
    <h3 onclick="editName(${i})">${p.name}</h3>
    <div class="price">₹${p.price}</div>
    <input id="q${i}" type="number" placeholder="Qty">
    <button class="addBtn" onclick="addCart(${i})">Add</button>
    ${admin ? `<button class="deleteBtn" onclick="deleteP(${i})">Delete</button>`:""}
  </div>`;
 });
}

/* EDIT */
function editName(i){
 if(!admin) return;
 let n=prompt("नया नाम",products[i].name);
 if(n){
  products[i].name=n;
  save();
  render();
 }
}

/* DELETE */
function deleteP(i){
 products.splice(i,1);
 save();
 render();
}

/* SEARCH */
function search(){
 let s=document.getElementById("searchBar").value.toLowerCase();
 document.querySelectorAll(".product").forEach(p=>{
  p.style.display=p.innerText.toLowerCase().includes(s)?"":"none";
 });
}

/* CART */
function addCart(i){
 let qty=document.getElementById("q"+i).value;
 if(!qty) return alert("Qty डालो");

 let p=products[i];

 cart.push({
  name:p.name,
  qty,
  total:p.price*qty
 });

 updateCart();
}

function updateCart(){
 let c=document.getElementById("cart");
 c.innerHTML="";
 let t=0;

 cart.forEach(x=>{
  t+=x.total;
  c.innerHTML+=`${x.name} - ${x.qty} = ₹${x.total}<br>`;
 });

 document.getElementById("total").innerText="₹"+t;
}

/* WHATSAPP */
function sendWhats(){
 let name = document.getElementById("custName").value || "Customer";

 let msg = "नाम: "+name+"%0AOrder:%0A";

 cart.forEach(x=>{
  msg+=`${x.name} ${x.qty} ₹${x.total}%0A`;
 });

 window.open("https://wa.me/918103166998?text="+msg);
}

/* LOAD */
window.onload=function(){
 let photo = localStorage.getItem("ownerPhoto");
 if(photo) ownerPhoto.src = photo;
 render();
}
</script>

</body>
</html>
