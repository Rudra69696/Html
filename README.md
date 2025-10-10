<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PasteFox - Simple Pastebin Clone</title>
    <style>
        body {
            font-family: 'Courier New', monospace;
            margin: 0;
            padding: 20px;
            background-color: #1e1e1e;
            color: #d4d4d4;
            line-height: 1.5;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: #2d2d2d;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
        }
        h1 {
            text-align: center;
            color: #569cd6;
            margin-bottom: 30px;
        }
        textarea {
            width: 100%;
            height: 300px;
            background: #1e1e1e;
            color: #d4d4d4;
            border: 1px solid #404040;
            border-radius: 4px;
            padding: 10px;
            font-family: inherit;
            font-size: 14px;
            resize: vertical;
        }
        .buttons {
            text-align: center;
            margin: 20px 0;
        }
        button {
            background: #0e639c;
            color: white;
            border: none;
            padding: 10px 20px;
            margin: 0 5px;
            border-radius: 4px;
            cursor: pointer;
            font-family: inherit;
        }
        button:hover {
            background: #1177bb;
        }
        .paste-id {
            background: #3d3d3d;
            padding: 10px;
            border-radius: 4px;
            margin: 20px 0;
            word-break: break-all;
        }
        .view-paste {
            margin-top: 20px;
            padding: 15px;
            background: #1e1e1e;
            border: 1px solid #404040;
            border-radius: 4px;
            white-space: pre-wrap;
            font-size: 14px;
        }
        .hidden { display: none; }
        .demo-note {
            font-size: 12px;
            color: #888;
            text-align: center;
            margin-top: 10px;
        }
        .raw-link {
            margin-top: 10px;
            font-size: 12px;
        }
        .raw-link a {
            color: #569cd6;
            text-decoration: none;
        }
        .view-buttons {
            text-align: center;
            margin-top: 15px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>PasteFox 🦊</h1>
        <p>Sly & quick: Paste your code or text, get a shareable link like pastefox.com/xyz123. (Demo uses local storage.)</p>
        
        <textarea id="codeInput" placeholder="Paste your content here... Supports code, notes, anything!"></textarea>
        
        <div class="buttons">
            <button onclick="createPaste()">Create Paste</button>
            <button onclick="clearAll()">Clear</button>
        </div>
        
        <div id="pasteResult" class="hidden">
            <h3>Your Paste Link:</h3>
            <div id="prettyUrl" class="paste-id"></div>
            <div class="raw-link">Raw View: <a href="#" id="rawLink" onclick="viewRaw(event)">Click here</a></div>
            <p class="demo-note">Demo: Share ?id=xyz123 on this page. For real hosting, use Netlify + functions!</p>
        </div>
        
        <div id="viewPaste" class="hidden">
            <h3>Paste Content:</h3>
            <div id="pasteContent" class="view-paste"></div>
            <div class="view-buttons">
                <button onclick="makeItRaw()">Make it Raw</button>
                <button onclick="backToEditor()">Back to Editor</button>
            </div>
        </div>
    </div>

    <script>
        const BASE_URL = 'https://pastefox.com/';
        let currentPasteContent = '';
        let currentPasteId = '';
        
        function generateId() {
            // Generate a more Pastebin-like ID: 7 random chars (alphanum)
            const chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
            let id = '';
            for (let i = 0; i < 7; i++) {
                id += chars.charAt(Math.floor(Math.random() * chars.length));
            }
            return id;
        }
        
        function createPaste() {
            const input = document.getElementById('codeInput');
            const content = input.value.trim();
            if (!content) {
                alert('Enter some content first!');
                return;
            }
            
            const id = generateId();
            localStorage.setItem('paste_' + id, content);
            currentPasteId = id;
            currentPasteContent = content;
            
            const result = document.getElementById('pasteResult');
            const prettyUrl = document.getElementById('prettyUrl');
            const rawLink = document.getElementById('rawLink');
            
            prettyUrl.innerHTML = `<strong>${BASE_URL}${id}</strong><br><small>Demo URL: <a href="?id=${id}">${window.location.href.split('?')[0]}?id=${id}</a></small>`;
            rawLink.href = `?id=${id}`;
            
            result.classList.remove('hidden');
            input.value = '';
            input.focus();
        }
        
        function loadPaste(id) {
            const content = localStorage.getItem('paste_' + id);
            if (content) {
                currentPasteContent = content;
                currentPasteId = id;
                document.getElementById('pasteContent').textContent = content;
                document.getElementById('viewPaste').classList.remove('hidden');
                document.getElementById('pasteResult').classList.add('hidden');
                document.getElementById('codeInput').classList.add('hidden');
                document.querySelector('.buttons').classList.add('hidden');
            } else {
                alert('Paste not found! Create a new one.');
            }
        }
        
        function viewRaw(event) {
            event.preventDefault();
            const urlParams = new URLSearchParams(window.location.search);
            const id = urlParams.get('id');
            if (id) loadPaste(id);
        }
        
        function makeItRaw() {
            // Create a downloadable raw text file
            const blob = new Blob([currentPasteContent], { type: 'text/plain' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `pastefox-${currentPasteId}.txt`;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
            alert('Raw paste downloaded as .txt file!');
        }
        
        function backToEditor() {
            document.getElementById('viewPaste').classList.add('hidden');
            document.getElementById('pasteResult').classList.add('hidden');
            document.getElementById('codeInput').classList.remove('hidden');
            document.querySelector('.buttons').classList.remove('hidden');
            document.getElementById('codeInput').focus();
        }
        
        function clearAll() {
            document.getElementById('codeInput').value = '';
            document.getElementById('pasteResult').classList.add('hidden');
            document.getElementById('viewPaste').classList.add('hidden');
            document.getElementById('codeInput').classList.remove('hidden');
            document.querySelector('.buttons').classList.remove('hidden');
            document.getElementById('codeInput').focus();
        }
        
        // Auto-load if URL has ?id=
        window.addEventListener('load', () => {
            const urlParams = new URLSearchParams(window.location.search);
            const id = urlParams.get('id');
            if (id) {
                loadPaste(id);
            } else {
                document.getElementById('codeInput').focus();
            }
        });
    </script>
</body>
</html>
