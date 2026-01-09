<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Mini Pastefy</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
    body {
        margin: 0;
        font-family: Arial, Helvetica, sans-serif;
        background: #0d1117;
        color: #c9d1d9;
    }
    header {
        padding: 15px;
        background: #161b22;
        text-align: center;
        font-size: 20px;
        font-weight: bold;
    }
    .container {
        max-width: 900px;
        margin: 20px auto;
        padding: 15px;
    }
    textarea {
        width: 100%;
        height: 300px;
        background: #010409;
        color: #c9d1d9;
        border: 1px solid #30363d;
        border-radius: 6px;
        padding: 10px;
        font-size: 14px;
        resize: vertical;
    }
    button {
        background: #238636;
        color: white;
        border: none;
        padding: 10px 18px;
        border-radius: 6px;
        cursor: pointer;
        margin-top: 10px;
        font-size: 14px;
    }
    button:hover {
        background: #2ea043;
    }
    .output {
        margin-top: 20px;
        padding: 10px;
        background: #161b22;
        border: 1px solid #30363d;
        border-radius: 6px;
        word-break: break-all;
    }
    a {
        color: #58a6ff;
        text-decoration: none;
    }
    a:hover {
        text-decoration: underline;
    }
</style>
</head>
<body>

<header>Mini Pastefy</header>

<div class="container">
    <textarea id="paste" placeholder="Paste your text or code here..."></textarea>
    <br>
    <button onclick="savePaste()">Create Paste</button>

    <div class="output" id="result" style="display:none;"></div>
</div>

<script>
function generateId(length = 8) {
    return Math.random().toString(36).substring(2, 2 + length);
}

function savePaste() {
    const text = document.getElementById("paste").value;
    if (!text.trim()) {
        alert("Paste cannot be empty!");
        return;
    }

    const id = generateId();
    localStorage.setItem("paste_" + id, text);

    const url = window.location.origin + window.location.pathname + "?id=" + id;
    const result = document.getElementById("result");
    result.style.display = "block";
    result.innerHTML = `Paste saved! <br><a href="${url}">${url}</a>`;
}

// Load paste if ?id=
const params = new URLSearchParams(window.location.search);
const pasteId = params.get("/");
if (pasteId) {
    const data = localStorage.getItem("paste_" + pasteId);
    if (data) {
        document.getElementById("paste").value = data;
    } else {
        alert("Paste not found!");
    }
}
</script>

</body>
</html>
