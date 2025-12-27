<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Lost & Found Image Matching System</title>

    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f2f2f2;
        }

        .container {
            width: 650px;
            margin: 30px auto;
            background: #ffffff;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            box-shadow: 0 0 10px rgba(0,0,0,0.2);
        }

        h2 {
            color: #333;
        }

        input[type="text"] {
            width: 80%;
            padding: 10px;
            margin: 15px 0;
            font-size: 16px;
        }

        .upload-section {
            display: flex;
            justify-content: space-around;
            margin: 20px 0;
        }

        .upload-box {
            width: 45%;
        }

        img {
            width: 150px;
            height: 150px;
            border: 1px solid #ccc;
            margin-top: 10px;
            object-fit: cover;
        }

        button {
            padding: 10px 25px;
            font-size: 16px;
            background: #007bff;
            color: white;
            border: none;
            cursor: pointer;
            border-radius: 5px;
        }

        button:hover {
            background: #0056b3;
        }

        #result {
            margin-top: 20px;
            font-size: 18px;
            font-weight: bold;
        }
    </style>
</head>

<body>

<div class="container">

    <h2>Automatic Lost & Found Image Matching System</h2>

    <!-- Username -->
    <input type="text" id="username" placeholder="Enter your name">

    <div class="upload-section">

        <div class="upload-box">
            <p><b>Upload Lost Item Image</b></p>
            <input type="file" id="lostImage" accept="image/*">
            <img id="lostPreview">
        </div>

        <div class="upload-box">
            <p><b>Upload Found Item Image</b></p>
            <input type="file" id="foundImage" accept="image/*">
            <img id="foundPreview">
        </div>

    </div>

    <button onclick="compareImages()">Compare Images</button>

    <div id="result"></div>

</div>

<script>
    const lostInput = document.getElementById("lostImage");
    const foundInput = document.getElementById("foundImage");

    lostInput.addEventListener("change", () => previewImage(lostInput, "lostPreview"));
    foundInput.addEventListener("change", () => previewImage(foundInput, "foundPreview"));

    function previewImage(input, previewId) {
        const file = input.files[0];
        if (file) {
            const reader = new FileReader();
            reader.onload = function () {
                document.getElementById(previewId).src = reader.result;
            };
            reader.readAsDataURL(file);
        }
    }

    function compareImages() {
        const userName = document.getElementById("username").value;

        if (userName === "") {
            alert("Please enter your name");
            return;
        }

        const img1 = document.getElementById("lostPreview");
        const img2 = document.getElementById("foundPreview");

        if (!img1.src || !img2.src) {
            alert("Please upload both images");
            return;
        }

        const canvas1 = document.createElement("canvas");
        const canvas2 = document.createElement("canvas");

        const ctx1 = canvas1.getContext("2d");
        const ctx2 = canvas2.getContext("2d");

        canvas1.width = canvas2.width = 150;
        canvas1.height = canvas2.height = 150;

        ctx1.drawImage(img1, 0, 0, 150, 150);
        ctx2.drawImage(img2, 0, 0, 150, 150);

        const data1 = ctx1.getImageData(0, 0, 150, 150).data;
        const data2 = ctx2.getImageData(0, 0, 150, 150).data;

        let difference = 0;
        for (let i = 0; i < data1.length; i += 4) {
            difference += Math.abs(data1[i] - data2[i]);
            difference += Math.abs(data1[i+1] - data2[i+1]);
            difference += Math.abs(data1[i+2] - data2[i+2]);
        }

        const threshold = 500000;
        const result = document.getElementById("result");

        if (difference < threshold) {
            result.innerHTML =
                "✅ Images are SAME <br><br>" +
                "Hello <b>" + userName + "</b>, please contact the person who lost the item.";
            result.style.color = "green";
        } else {
            result.innerHTML = "❌ Images are DIFFERENT";
            result.style.color = "red";
        }
    }
</script>

</body>
</html>
