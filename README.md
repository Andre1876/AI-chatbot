# AI-chatbot
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Chatbot</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
        }

        #chat-container {
            width: 100%;
            max-width: 600px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            overflow: hidden;
        }

        #chat-box {
            height: 400px;
            overflow-y: scroll;
            padding: 10px;
            border-bottom: 2px solid #ddd;
        }

        #chat-input-container {
            display: flex;
            padding: 10px;
            background-color: #f7f7f7;
            border-top: 2px solid #ddd;
        }

        #chat-input {
            width: 90%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 16px;
        }

        #send-button {
            width: 10%;
            padding: 10px;
            border: none;
            background-color: #007BFF;
            color: white;
            font-size: 16px;
            cursor: pointer;
            border-radius: 5px;
        }

        #send-button:hover {
            background-color: #0056b3;
        }

        .message {
            margin: 10px 0;
            padding: 10px;
            border-radius: 5px;
        }

        .user-message {
            background-color: #e6f7ff;
            text-align: right;
        }

        .bot-message {
            background-color: #f1f1f1;
        }
    </style>
</head>
<body>
    <div id="chat-container">
        <div id="chat-box">
            <!-- Chat messages will appear here -->
        </div>
        <div id="chat-input-container">
            <input type="text" id="chat-input" placeholder="Type your message...">
            <button id="send-button">Send</button>
        </div>
    </div>

    <script>
        const apiKey = "your-openai-api-key-here"; // Insert your OpenAI API key here
        const chatBox = document.getElementById('chat-box');
        const chatInput = document.getElementById('chat-input');
        const sendButton = document.getElementById('send-button');

        // Function to display messages in the chat box
        function displayMessage(message, sender) {
            const messageElement = document.createElement('div');
            messageElement.classList.add('message');
            messageElement.classList.add(sender === 'user' ? 'user-message' : 'bot-message');
            messageElement.innerText = message;
            chatBox.appendChild(messageElement);
            chatBox.scrollTop = chatBox.scrollHeight; // Scroll to the bottom
        }

        // Function to send the user message and get the AI response
        async function sendMessage() {
            const userMessage = chatInput.value.trim();
            if (userMessage === "") return;

            displayMessage(userMessage, 'user');
            chatInput.value = ""; // Clear input field

            // Show typing indicator for the bot
            displayMessage("Thinking...", 'bot');

            try {
                const response = await fetch("https://api.openai.com/v1/completions", {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": `Bearer ${apiKey}`,
                    },
                    body: JSON.stringify({
                        model: "text-davinci-003", // Or use another model if needed
                        prompt: userMessage,
                        max_tokens: 150,
                    })
                });

                const data = await response.json();
                const botMessage = data.choices[0].text.trim();

                // Update the bot's message
                const lastBotMessage = chatBox.lastChild;
                if (lastBotMessage && lastBotMessage.innerText === "Thinking...") {
                    lastBotMessage.innerText = botMessage;
                } else {
                    displayMessage(botMessage, 'bot');
                }
            } catch (error) {
                console.error("Error:", error);
                displayMessage("Sorry, I couldn't process your request.", 'bot');
            }
        }

        // Event listeners for send button and input field
        sendButton.addEventListener('click', sendMessage);
        chatInput.addEventListener('keypress', function(event) {
            if (event.key === 'Enter') {
                sendMessage();
            }
        });
    </script>
</body>
</html>
