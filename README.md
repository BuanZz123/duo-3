<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Random CC Generator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
        }
        table {
            margin: 20px auto;
            border-collapse: collapse;
            width: 80%;
        }
        th, td {
            border: 1px solid black;
            padding: 10px;
            text-align: center;
        }
        button {
            padding: 10px 20px;
            margin: 10px;
            font-size: 16px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h2>Random Credit Card Generator</h2>
    <label>BIN (4-16 digits, optional): <input type="text" id="bin" maxlength="16"></label><br>
    <label>Expiry Month: 
        <select id="expMonth">
            <option value="">Random</option>
            <script>
                for (let i = 1; i <= 12; i++) {
                    document.write(`<option value="${i.toString().padStart(2, '0')}">${i.toString().padStart(2, '0')}</option>`);
                }
            </script>
        </select>
    </label><br>
    <label>Expiry Year: 
        <select id="expYear">
            <option value="">Random</option>
            <script>
                for (let i = 2025; i <= 2030; i++) {
                    document.write(`<option value="${i}">${i}</option>`);
                }
            </script>
        </select>
    </label><br>
    <label>CVV/CVC (optional): <input type="text" id="cvv" maxlength="3"></label><br>
    <label>Quantity (max 500): <input type="number" id="quantity" min="1" max="500" value="1"></label><br><br>
    <button onclick="generateCard('visa')">Generate Visa</button>
    <button onclick="generateCard('mastercard')">Generate Mastercard</button>
    <button onclick="copyToClipboard()">Copy</button>
    <button onclick="downloadList()">Download</button>
    <table id="output"></table>
    
    <script>
        function generateCard(type) {
            let bin = document.getElementById("bin").value;
            let expMonth = document.getElementById("expMonth").value || (Math.floor(Math.random() * 12) + 1).toString().padStart(2, '0');
            let expYear = document.getElementById("expYear").value || (Math.floor(Math.random() * 6) + 2025);
            let cvv = document.getElementById("cvv").value || Math.floor(100 + Math.random() * 900);
            let quantity = Math.min(parseInt(document.getElementById("quantity").value), 500);
            
            if (!bin) {
                bin = type === 'visa' ? '4' : '5' + Math.floor(Math.random() * 5 + 1);
            }
            
            let result = "";
            let table = document.getElementById("output");
            table.innerHTML = "<tr><th>Card Number</th><th>Expiry</th><th>CVV</th></tr>";
            
            for (let i = 0; i < quantity; i++) {
                let cardNumber = bin;
                while (cardNumber.length < 15) {
                    cardNumber += Math.floor(Math.random() * 10);
                }
                cardNumber += calculateLuhnChecksum(cardNumber);
                result += `${cardNumber}|${expMonth}|${expYear}|${cvv}\n`;
                
                let row = table.insertRow();
                row.insertCell(0).innerText = cardNumber;
                row.insertCell(1).innerText = `${expMonth}/${expYear}`;
                row.insertCell(2).innerText = cvv;
            }
            document.getElementById("output").dataset.cards = result;
        }
        
        function calculateLuhnChecksum(number) {
            let sum = 0;
            let alternate = true;
            
            for (let i = number.length - 1; i >= 0; i--) {
                let n = parseInt(number[i], 10);
                
                if (alternate) {
                    n *= 2;
                    if (n > 9) n -= 9;
                }
                sum += n;
                alternate = !alternate;
            }
            return (sum * 9) % 10;
        }
        
        function copyToClipboard() {
            let text = document.getElementById("output").dataset.cards || "";
            if (!text) return alert("No data to copy");
            
            navigator.clipboard.writeText(text).then(() => {
                alert("Copied to clipboard");
            }, () => {
                alert("Failed to copy");
            });
        }
        
        function downloadList() {
            let text = document.getElementById("output").dataset.cards || "";
            if (!text) return alert("No data to download");
            
            let blob = new Blob([text], { type: "text/plain" });
            let link = document.createElement("a");
            link.href = URL.createObjectURL(blob);
            link.download = "card_list.txt";
            link.click();
        }
    </script>
</body>
</html>
