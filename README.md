<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Lost & Found Image Matching</title>

<style>
    body {
        margin: 0;
        font-family: Arial, sans-serif;
        background: linear-gradient(135deg, #667eea, #764ba2);
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .card {
        background: white;
        width: 700px;
        padding: 25px;
        border-radius: 15px;
        box-shadow: 0 20px 40px rgba(0,0,0,0.3);
        display: none;
    }

    h2 {
        text-align: center;
        color: #333;
    }

    input[type="text"], input[type="file"] {
        width: 90%;
        padding: 12px;
        margin: 10px 0;
        font-size: 16px;
    }

    button {
        padding: 12px 25px;
        font-size: 18px;
        background: #667eea;
        color: white;
        border: none;
        border-radius: 6px;
        cursor: pointer;
        display: block;
        margin: 20px auto;
    }

    button:hover {
        background: #5a67d8;
    }

    .upload-section {
        display: flex;
        justify-content: space-around;
        margin-top: 20px;
    }

    img {
        width: 150px;
        height: 150px;
        border: 1px solid #ccc;
        margin-top: 10px;
        object-fit: cover;
        border-radius: 8px;
    }

    #result {
        text-align: center;
        font-size: 18px;
        font-weight: bold;
        margin-top: 15px;
    }

    .welcome {
        text-align: center;
        font-size: 18px;
        margin-bottom: 10px;
    }
</style>
</head>

<body>

<!-- LOGIN PAGE -->
<div class="card" id="loginPage" style="display:block;">
    <h2>Lost & Found System</h2>
    <input type="text" id="username" placeholder="Enter your name">
    <button onclick="login()">Login</button>
</div>

<!-- MATCHING PAGE -->
<div class="card" id="matchPage">
    <div class="welcome">Welcome, <b id="user"></b></div>
    <h2>Image Matching</h2>

    <div class="upload-section">
        <div>
            <p><b>Lost Item Image</b></p>
            <input type="file" id="lostImage" accept="image/*">
            <img id="lostPreview">
        </div>

        <div>
            <p><b>Found Item Image</b></p>
            <input type="file" id="foundImage" accept="image/*">
            <img id="foundPreview">
        </div>
    </div>

    <button onclick="compareImages()">Compare Images</button>
    <div id="result"></div>
</div>

<script>
    let currentUser = "";

    function login() {
        const name = document.getElementById("username").value.trim();
        if (name === "") {
            alert("Please enter your name");
            return;
        }
        currentUser = name;
        document.getElementById("user").innerText = name;
        document.getElementById("loginPage").style.display = "none";
        document.getElementById("matchPage").style.display = "block";
    }

    document.getElementById("lostImage").onchange = () =>
        preview("lostImage", "lostPreview");

    document.getElementById("foundImage").onchange = () =>
        preview("foundImage", "foundPreview");

    function preview(inputId, previewId) {
        const file = document.getElementById(inputId).files[0];
        if (file) {
            const reader = new FileReader();
            reader.onload = () =>
                document.getElementById(previewId).src = reader.result;
            reader.readAsDataURL(file);
        }
    }

    function compareImages() {
        const img1 = document.getElementById("lostPreview");
        const img2 = document.getElementById("foundPreview");

        if (!img1.src || !img2.src) {
            alert("Upload both images");
            return;
        }

        const c1 = document.createElement("canvas");
        const c2 = document.createElement("canvas");
        c1.width = c2.width = 150;
        c1.height = c2.height = 150;

        const x1 = c1.getContext("2d");
        const x2 = c2.getContext("2d");

        x1.drawImage(img1, 0, 0, 150, 150);
        x2.drawImage(img2, 0, 0, 150, 150);

        const d1 = x1.getImageData(0,0,150,150).data;
        const d2 = x2.getImageData(0,0,150,150).data;

        let diff = 0;
        for (let i = 0; i < d1.length; i += 4) {
            diff += Math.abs(d1[i] - d2[i]);
            diff += Math.abs(d1[i+1] - d2[i+1]);
            diff += Math.abs(d1[i+2] - d2[i+2]);
        }

        const result = document.getElementById("result");

        if (diff < 500000) {
            result.innerHTML =
                "✅ Images are SAME <br>" +
                "Hello <b>" + currentUser +
                "</b>, please contact the person who lost the item.";
            result.style.color = "green";
        } else {
            result.innerHTML = "❌ Images are DIFFERENT";
            result.style.color = "red";
        }
    }
</script>

</body>
</html>
