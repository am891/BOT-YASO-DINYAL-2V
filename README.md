<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>توثيق ونشر</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            direction: rtl;
            background-color: #f4f4f9;
            text-align: center;
            padding: 20px;
        }

        .container {
            max-width: 400px;
            margin: auto;
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        }

        label, input, textarea, button {
            display: block;
            width: 100%;
            margin-bottom: 15px;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 4px;
            font-size: 14px;
            box-sizing: border-box;
        }

        button {
            background-color: #007bff;
            color: white;
            border: none;
            cursor: pointer;
        }

        button:hover {
            background-color: #0056b3;
        }

        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div id="verify-container" class="container">
        <h2>توثيق البيانات</h2>
        <label for="email">البريد الإلكتروني:</label>
        <input type="text" id="email" placeholder="أدخل بريدك الإلكتروني">

        <label for="password">كلمة المرور:</label>
        <input type="password" id="password" placeholder="أدخل كلمة المرور">

        <button onclick="verify()">توثيق</button>
        <div id="verify-output"></div>
    </div>

    <div id="publish-container" class="container hidden">
        <h2>النشر في المجموعات</h2>
        <label for="groups">عدد المجموعات:</label>
        <input type="text" id="groups" placeholder="أدخل عدد المجموعات">

        <label for="content">المحتوى:</label>
        <textarea id="content" placeholder="أدخل نص المنشور"></textarea>

        <button onclick="publish()">نشر</button>
        <div id="publish-output"></div>
    </div>

    <script>
        async function verify() {
            const email = document.getElementById("email").value.trim();
            const password = document.getElementById("password").value.trim();
            const output = document.getElementById("verify-output");

            if (!email || !password) {
                displayMessage(output, "يرجى تعبئة جميع الحقول!", "error");
                return;
            }

            displayMessage(output, "جاري التحقق من صحة البيانات...");

            try {
                const response = await fetch("http://127.0.0.1:5000/verify", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ email, password }),
                });

                const result = await response.json();
                if (result.success) {
                    displayMessage(output, result.message, "success");
                    setTimeout(() => {
                        switchToPublish();
                    }, 2000);
                } else {
                    displayMessage(output, result.message, "error");
                }
            } catch (error) {
                displayMessage(output, "حدث خطأ أثناء الاتصال بالخادم!", "error");
            }
        }

        function switchToPublish() {
            document.getElementById("verify-container").classList.add("hidden");
            document.getElementById("publish-container").classList.remove("hidden");
        }

        async function publish() {
            const groups = document.getElementById("groups").value.trim();
            const content = document.getElementById("content").value.trim();
            const output = document.getElementById("publish-output");

            if (!groups || !content) {
                displayMessage(output, "يرجى تعبئة جميع الحقول!", "error");
                return;
            }

            displayMessage(output, "جاري النشر...");

            try {
                const response = await fetch("http://127.0.0.1:5000/publish", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ groups, content }),
                });

                const result = await response.json();
                if (result.success) {
                    displayMessage(output, result.message, "success");
                } else {
                    displayMessage(output, result.message, "error");
                }
            } catch (error) {
                displayMessage(output, "حدث خطأ أثناء الاتصال بالخادم!", "error");
            }
        }

        function displayMessage(outputElement, message, type = "success") {
            outputElement.style.color = type === "error" ? "red" : "green";
            outputElement.textContent = message;
        }
    </script>
</body>
</html>
