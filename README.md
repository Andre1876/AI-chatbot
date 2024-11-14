<!DOCTYPE html>
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
            background-color: #f4f7f6;
        }

        #chat-container {
            width: 100%;
            max-width: 600px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
            overflow: hidden;
            display: flex;
            flex-direction: column;
            height: 80vh;
        }

        #chat-box {
            height: 70%;
            overflow-y: scroll;
            padding: 15px;
            border-bottom: 2px solid #eee;
            background-color: #fafafa;
        }

        #chat-input-container {
            display: flex;
            padding: 10px;
            background-color: #fff;
            border-top: 2px solid #eee;
        }

        #chat-input {
            width: 85%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 16px;
            background-color: #f9f9f9;
        }

        #Go-button {
            width: 15%;
            padding: 10px;
            border: none;
            background-color: #007BFF;
            color: white;
            font-size: 16px;
            cursor: pointer;
            border-radius: 5px;
        }

        #Go-button:hover {
            background-color: #0056b3;
        }

        .message {
            margin: 10px 0;
            padding: 10px;
            border-radius: 5px;
            max-width: 80%;
            word-wrap: break-word;
        }

        .user-message {
            background-color: #e6f7ff;
            margin-left: auto;
            color: #007BFF;
            text-align: right;
        }

        .bot-message {
            background-color: #f1f1f1;
            color: #333;
            text-align: left;
        }

        .thinking {
            font-style: italic;
            color: gray;
        }

        .error {
            background-color: #f8d7da;
            color: #721c24;
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
        function displayMessage(message, sender, className = '') {
            const messageElement = document.createElement('div');
            messageElement.classList.add('message');
            messageElement.classList.add(sender === 'user' ? 'user-message' : 'bot-message');
            messageElement.classList.add(className);
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

            // Show thinking indicator for the bot
            displayMessage("Thinking...", 'bot', 'thinking');

            try {
                const response = await fetch("https://api.openai.com/v1/completions", {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": `Bearer ${apiKey}`,
                    },
                    body: JSON.stringify({
                        model: "text-davinci-003", // You can change this to other models like 'gpt-3.5-turbo' if needed
                        prompt: userMessage,
                        max_tokens: 150,
                        temperature: 0.7,  // Controls randomness
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
                displayMessage("Sorry, I couldn't process your request.", 'bot', 'error');
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
