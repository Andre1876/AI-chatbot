import socket
import threading
import time
import random

# Server class to handle incoming client connections
class NetworkServer:
    def __init__(self, host='localhost', port=8080):
        self.server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.server.bind((host, port))
        self.server.listen()
        print(f"Server started on {host}:{port}")
    
    def handle_client(self, client_socket, client_address):
        print(f"New connection from {client_address}")
        while True:
            try:
                message = client_socket.recv(1024).decode()
                if not message:
                    break
                print(f"Received from {client_address}: {message}")
                response = f"Echo: {message}"
                client_socket.send(response.encode())
            except ConnectionResetError:
                break
        print(f"Connection with {client_address} closed")
        client_socket.close()
    
    def start(self):
        print("Server is running and waiting for connections...")
        while True:
            client_socket, client_address = self.server.accept()
            client_handler = threading.Thread(
                target=self.handle_client, args=(client_socket, client_address)
            )
            client_handler.start()

# Client class to simulate network traffic
class NetworkClient:
    def __init__(self, host='localhost', port=8080):
        self.client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.client.connect((host, port))
        print("Connected to server")
    
    def send_data(self):
        try:
            for i in range(5):  # Send 5 messages as part of the traffic simulation
                message = f"Message {i+1} - Traffic data: {random.randint(100, 999)}"
                print(f"Sending: {message}")
                self.client.send(message.encode())
                response = self.client.recv(1024).decode()
                print(f"Received from server: {response}")
                time.sleep(random.uniform(0.5, 1.5))  # Random delay to simulate traffic intervals
        finally:
            print("Closing client connection")
            self.client.close()

# Main function to run the simulation
def run_simulation():
    # Start the server
    server = NetworkServer()
    server_thread = threading.Thread(target=server.start)
    server_thread.start()

    # Simulate multiple clients connecting to the server
    clients = []
    for i in range(3):  # Adjust the number of clients as needed
        client = NetworkClient()
        client_thread = threading.Thread(target=client.send_data)
        client_thread.start()
        clients.append(client_thread)

    # Wait for all client threads to complete
    for client_thread in clients:
        client_thread.join()

if __name__ == "__main__":
    run_simulation()
