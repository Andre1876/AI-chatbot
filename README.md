<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Network Traffic Simulation</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    #messages { border: 1px solid #ddd; padding: 10px; height: 200px; overflow-y: scroll; }
    .sent { color: blue; }
    .received { color: green; }
    .status { color: gray; }
  </style>
</head>
<body>
  <h2>Network Traffic Simulation</h2>
  <div id="messages"></div>
  <input type="text" id="messageInput" placeholder="Enter message to send" />
  <button onclick="sendMessage()">Send Message</button>

  <script>
    // Create a WebSocket to simulate network communication
    let socket = new WebSocket("wss://echo.websocket.org"); // Using a public WebSocket echo server
    const messagesDiv = document.getElementById("messages");

    // Display connection status
    socket.onopen = () => addMessage("Connected to server", "status");
    socket.onclose = () => addMessage("Disconnected from server", "status");

    // Function to display messages in the messages div
    function addMessage(message, type) {
      const msg = document.createElement("p");
      msg.classList.add(type);
      msg.textContent = message;
      messagesDiv.appendChild(msg);
      messagesDiv.scrollTop = messagesDiv.scrollHeight; // Auto-scroll to latest message
    }

    // Function to send messages to the server
    function sendMessage() {
      const messageInput = document.getElementById("messageInput");
      const message = messageInput.value.trim();
      if (message) {
        addMessage(`Sent: ${message}`, "sent");
        socket.send(message);
        messageInput.value = "";
      }
    }

    // Display messages received from the server
    socket.onmessage = (event) => {
      addMessage(`Received: ${event.data}`, "received");
    };
  </script>
</body>
</html>
