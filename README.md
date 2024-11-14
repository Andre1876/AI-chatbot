# AI-chatbot
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Chatbot</title>
    <style>
        /* Styling for the chatbot */
        body { font-family: Arial, sans-serif; }
        #chat { width: 300px; height: 400px; overflow-y: scroll; border: 1px solid #ccc; padding: 10px; }
        .message { margin: 10px 0; }
        .user { text-align: right; color: blue; }
        .bot { text-align: left; color: green; }
        input[type="text"] { width: 70%; padding: 10px; }
        button { padding: 10px; }
    </style>
</head>
<body>
    <h2>AI Chatbot</h2>
    <div id="chat"></div>
    <input type="text" id="messageInput" placeholder="Type a message...">
    <button onclick="sendMessage()">Send</button>

    <script>
        const chatBox = document.getElementById("chat");

        // Display messages in the chat window
        function displayMessage(message, sender) {
            const msgElement = document.createElement("div");
            msgElement.classList.add("message");
            msgElement.classList.add(sender);
            msgElement.innerText = message;
            chatBox.appendChild(msgElement);
            chatBox.scrollTop = chatBox.scrollHeight;
        }

        // Send user message to OpenAI API and get the response
        function sendMessage() {
            const userMessage = document.getElementById("messageInput").value;
            if (userMessage.trim() === "") return;

            displayMessage(userMessage, "user");
            document.getElementById("messageInput").value = "";

            // Replace this with your OpenAI API Key
            const apiKey = "YOUR_OPENAI_API_KEY";

            // Send request to OpenAI API
            fetch("https://api.openai.com/v1/completions", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    "Authorization": `Bearer ${apiKey}`
                },
                body: JSON.stringify({
                    model: "text-davinci-003",
                    prompt: userMessage,
                    max_tokens: 150
                })
            })
            .then(response => response.json())
            .then(data => {
                const botResponse = data.choices[0].text.trim();
                displayMessage(botResponse, "bot");
            })
            .catch(error => {
                console.error("Error:", error);
                displayMessage("Sorry, I couldn't process your request.", "bot");
            });
        }
    </script>
</body>
</html>
