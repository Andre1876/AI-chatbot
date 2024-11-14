
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Network Traffic Simulation</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    #messages { border: 1px solid #ddd; padding: 10px; height: 200px; overflow-y: scroll; margin-bottom: 10px; }
    .sent { color: blue; }
    .received { color: green; }
    .status { color: gray; }
    input, button { padding: 8px; margin-top: 5px; }
  </style>
</head>
<body>
  
  
  <!-- Messages container -->
  <div id="messages"></div>
  
  <!-- User input and send button -->
  <input type="text" id="messageInput" placeholder="Enter message to send" />
  <button onclick="sendMessage()">Send Message</button>
  
  <script>
    // Initialize WebSocket connection (public WebSocket echo server)
    let socket = new WebSocket("wss://echo.websocket.org"); // Using a public WebSocket echo server
    
    const messagesDiv = document.getElementById("messages");
    const messageInput = document.getElementById("messageInput");

    // Display connection status
    socket.onopen = () => addMessage("Connected to WebSocket server.", "status");
    socket.onclose = () => addMessage("Disconnected from WebSocket server.", "status");
    
    // Display received messages
    socket.onmessage = (event) => {
      addMessage(`Received: ${event.data}`, "received");
    };

    // Function to add messages to the UI
    function addMessage(message, type) {
      const msg = document.createElement("p");
      msg.classList.add(type);
      msg.textContent = message;
      messagesDiv.appendChild(msg);
      messagesDiv.scrollTop = messagesDiv.scrollHeight; // Auto-scroll to latest message
    }

    // Function to simulate network traffic (send messages with delay)
    function sendMessage() {
      const message = messageInput.value.trim();
      if (message) {
        // Simulate network delay of 2 seconds
        addMessage(`Sent: ${message}`, "sent");
        
        // Simulate delay before sending message
        setTimeout(() => {
          socket.send(message);
        }, 2000); // 2-second delay
        messageInput.value = ""; // Clear input after sending
      }
    }

    // Handle socket errors
    socket.onerror = (error) => addMessage(`Error: ${error.message}`, "status");

    // Simulate a network timeout
    setTimeout(() => {
      addMessage("Simulating network timeout (no response from server)", "status");
    }, 10000); // Timeout after 10 seconds if no response

  </script>
</body>
</html>
