# WebSocket Protocol

## Overview
WebSocket is a computer communications protocol that provides full-duplex communication channels over a single TCP connection. It enables real-time, bidirectional communication between web clients and servers, making it ideal for applications requiring live data feeds, chat functionality, or real-time updates.

## Technical Details

### Protocol Characteristics
- Transport: TCP
- Default Ports: 80 (ws://) or 443 (wss://)
- Handshake: HTTP upgrade request
- Message Types: Text and Binary
- Frame Size: Up to 2^63 bytes
- Extensions: Compression, multiplexing
- Subprotocols: Custom application protocols

### Core Components
1. **Handshake**: Initial HTTP upgrade request
2. **Frames**: Basic protocol data units
3. **Messages**: Complete sequences of frames
4. **Connection**: Persistent TCP connection
5. **Close Frame**: Graceful connection termination

## Implementation

### Server Implementation (Python with FastAPI and WebSockets)
```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from typing import List
import json

app = FastAPI()

class ConnectionManager:
    def __init__(self):
        self.active_connections: List[WebSocket] = []
        
    async def connect(self, websocket: WebSocket):
        await websocket.accept()
        self.active_connections.append(websocket)
        
    def disconnect(self, websocket: WebSocket):
        self.active_connections.remove(websocket)
        
    async def broadcast(self, message: str):
        for connection in self.active_connections:
            await connection.send_text(message)
            
    async def send_personal_message(self, message: str, websocket: WebSocket):
        await websocket.send_text(message)

manager = ConnectionManager()

@app.websocket("/ws/{client_id}")
async def websocket_endpoint(websocket: WebSocket, client_id: str):
    await manager.connect(websocket)
    try:
        while True:
            data = await websocket.receive_text()
            # Process received message
            message = json.loads(data)
            
            # Handle different message types
            if message.get('type') == 'broadcast':
                await manager.broadcast(f"Client {client_id}: {message['content']}")
            else:
                await manager.send_personal_message(
                    f"You wrote: {message['content']}", websocket
                )
                
    except WebSocketDisconnect:
        manager.disconnect(websocket)
        await manager.broadcast(f"Client {client_id} left the chat")
```

### Client Implementation (JavaScript)
```javascript
class WebSocketClient {
    constructor(url) {
        this.url = url;
        this.ws = null;
        this.reconnectAttempts = 0;
        this.maxReconnectAttempts = 5;
        this.reconnectDelay = 1000; // Start with 1 second
    }
    
    connect() {
        this.ws = new WebSocket(this.url);
        
        this.ws.onopen = () => {
            console.log('Connected to WebSocket server');
            this.reconnectAttempts = 0;
            this.reconnectDelay = 1000;
            
            // Send authentication message
            this.send({
                type: 'auth',
                token: 'your-auth-token'
            });
        };
        
        this.ws.onmessage = (event) => {
            try {
                const message = JSON.parse(event.data);
                this.handleMessage(message);
            } catch (error) {
                console.error('Error parsing message:', error);
            }
        };
        
        this.ws.onclose = () => {
            console.log('WebSocket connection closed');
            this.attemptReconnect();
        };
        
        this.ws.onerror = (error) => {
            console.error('WebSocket error:', error);
        };
    }
    
    handleMessage(message) {
        switch (message.type) {
            case 'chat':
                this.handleChatMessage(message);
                break;
            case 'notification':
                this.handleNotification(message);
                break;
            case 'error':
                this.handleError(message);
                break;
            default:
                console.log('Unknown message type:', message.type);
        }
    }
    
    send(data) {
        if (this.ws && this.ws.readyState === WebSocket.OPEN) {
            this.ws.send(JSON.stringify(data));
        } else {
            console.error('WebSocket is not connected');
        }
    }
    
    attemptReconnect() {
        if (this.reconnectAttempts < this.maxReconnectAttempts) {
            setTimeout(() => {
                console.log('Attempting to reconnect...');
                this.connect();
                this.reconnectAttempts++;
                this.reconnectDelay *= 2; // Exponential backoff
            }, this.reconnectDelay);
        } else {
            console.error('Max reconnection attempts reached');
        }
    }
    
    close() {
        if (this.ws) {
            this.ws.close();
        }
    }
}

// Usage example
const client = new WebSocketClient('ws://localhost:8000/ws/client123');
client.connect();

// Send message
client.send({
    type: 'chat',
    content: 'Hello, everyone!'
});
```

### HTML Implementation
```html
<!DOCTYPE html>
<html>
<head>
    <title>WebSocket Chat</title>
    <style>
        #messages {
            height: 300px;
            overflow-y: auto;
            border: 1px solid #ccc;
            padding: 10px;
            margin-bottom: 10px;
        }
        .message {
            margin: 5px 0;
            padding: 5px;
            border-radius: 5px;
        }
        .sent {
            background-color: #e3f2fd;
            margin-left: 20%;
        }
        .received {
            background-color: #f5f5f5;
            margin-right: 20%;
        }
    </style>
</head>
<body>
    <div id="messages"></div>
    <input type="text" id="messageInput" placeholder="Type a message...">
    <button onclick="sendMessage()">Send</button>

    <script>
        const messagesDiv = document.getElementById('messages');
        const messageInput = document.getElementById('messageInput');
        const client = new WebSocketClient('ws://localhost:8000/ws/client123');
        
        function sendMessage() {
            const message = messageInput.value;
            if (message) {
                client.send({
                    type: 'chat',
                    content: message
                });
                addMessage(message, true);
                messageInput.value = '';
            }
        }
        
        function addMessage(message, sent) {
            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${sent ? 'sent' : 'received'}`;
            messageDiv.textContent = message;
            messagesDiv.appendChild(messageDiv);
            messagesDiv.scrollTop = messagesDiv.scrollHeight;
        }
        
        // Handle received messages
        client.handleChatMessage = (message) => {
            addMessage(message.content, false);
        };
    </script>
</body>
</html>
```

## Design Considerations

### Architecture Planning
1. **Connection Management**
   - Handle multiple connections
   - Implement heartbeat mechanism
   - Manage connection pools
   - Handle reconnection logic

2. **Message Format**
   - Define message structure
   - Implement message validation
   - Handle different message types
   - Consider compression

3. **Scaling**
   - Load balancing
   - Connection distribution
   - State management
   - Message queuing

## Security Implementation

### Secure WebSocket (WSS)
```python
# Server (Python with SSL/TLS)
import ssl
from fastapi import FastAPI
import uvicorn

app = FastAPI()

if __name__ == "__main__":
    ssl_context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
    ssl_context.load_cert_chain('server.crt', 'server.key')
    
    uvicorn.run(
        app,
        host="0.0.0.0",
        port=8443,
        ssl_keyfile="server.key",
        ssl_certfile="server.crt"
    )
```

### Authentication and Authorization
```python
from fastapi import Depends, HTTPException
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.websocket("/ws/{client_id}")
async def websocket_endpoint(
    websocket: WebSocket,
    client_id: str,
    token: str = Depends(oauth2_scheme)
):
    if not verify_token(token):
        await websocket.close(code=1008)  # Policy violation
        return
        
    await manager.connect(websocket)
    # ... rest of the implementation
```

## Best Practices

### Implementation Guidelines
1. Implement proper error handling
2. Use heartbeat mechanisms
3. Handle reconnection gracefully
4. Validate messages
5. Implement proper logging

### Monitoring and Maintenance
```python
class WebSocketMonitor:
    def __init__(self):
        self.connections = {}
        self.message_count = 0
        
    def track_connection(self, client_id):
        self.connections[client_id] = {
            'connected_at': time.time(),
            'message_count': 0,
            'last_message_at': None
        }
        
    def track_message(self, client_id):
        if client_id in self.connections:
            self.connections[client_id]['message_count'] += 1
            self.connections[client_id]['last_message_at'] = time.time()
            self.message_count += 1
            
    def get_stats(self):
        return {
            'total_connections': len(self.connections),
            'total_messages': self.message_count,
            'connections': self.connections
        }
```

### Error Handling
1. Connection failures
2. Message parsing errors
3. Authentication failures
4. Rate limiting
5. Resource cleanup

## Interview Tips

### Key Concepts
1. WebSocket handshake process
2. Full-duplex communication
3. Frame types and message formats
4. Connection management
5. Security considerations

### Common Questions
1. How does WebSocket differ from HTTP?
2. What is the WebSocket handshake process?
3. How to handle connection failures?
4. What security measures should be implemented?
5. How to scale WebSocket applications?

### Real-world Scenarios
1. Real-time chat applications
2. Live dashboard updates
3. Collaborative editing
4. Gaming applications
5. Financial data feeds 