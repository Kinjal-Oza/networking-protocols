# WebSocket

## Overview
WebSocket is a communication protocol that provides full-duplex communication channels over a single TCP connection. It enables real-time, bidirectional communication between web clients and servers, making it ideal for applications requiring live data feeds, chat functionality, or real-time updates.

## Technical Details

### Protocol Characteristics
- Persistent TCP connection
- Full-duplex communication
- Low latency
- Protocol prefix: `ws://` or `wss://` (secure)
- Default ports: 80 (ws) / 443 (wss)
- Minimal overhead after handshake
- Built-in subprotocol support
- Cross-origin communication

### WebSocket Handshake

1. **Client Request**
   ```http
   GET /chat HTTP/1.1
   Host: server.example.com
   Upgrade: websocket
   Connection: Upgrade
   Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
   Origin: http://example.com
   Sec-WebSocket-Protocol: chat, superchat
   Sec-WebSocket-Version: 13
   ```

2. **Server Response**
   ```http
   HTTP/1.1 101 Switching Protocols
   Upgrade: websocket
   Connection: Upgrade
   Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
   Sec-WebSocket-Protocol: chat
   ```

### Frame Format
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-------+-+-------------+-------------------------------+
|F|R|R|R| opcode|M| Payload len |    Extended payload length    |
|I|S|S|S|  (4)  |A|     (7)    |             (16/64)          |
|N|V|V|V|       |S|             |   (if payload len==126/127)  |
| |1|2|3|       |K|             |                               |
+-+-+-+-+-------+-+-------------+-------------------------------+
|     Extended payload length continued, if payload len == 127  |
+-------------------------------+-------------------------------+
|                               |Masking-key, if MASK set to 1 |
+-------------------------------+-------------------------------+
| Masking-key (continued)       |          Payload Data        |
+-------------------------------- - - - - - - - - - - - - - - -
:                     Payload Data continued ...                :
+ - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
|                     Payload Data continued ...                |
+---------------------------------------------------------------+
```

## Implementation

### Server Implementation (Node.js)

1. **Basic WebSocket Server**
   ```javascript
   const WebSocket = require('ws');
   
   const wss = new WebSocket.Server({ port: 8080 });
   
   wss.on('connection', (ws) => {
     console.log('New client connected');
     
     ws.on('message', (message) => {
       console.log('Received:', message);
       
       // Echo back to client
       ws.send(`Server received: ${message}`);
     });
     
     ws.on('close', () => {
       console.log('Client disconnected');
     });
     
     ws.on('error', (error) => {
       console.error('WebSocket error:', error);
     });
   });
   ```

2. **Advanced Server Features**
   ```javascript
   const WebSocket = require('ws');
   const https = require('https');
   const fs = require('fs');
   
   // Secure WebSocket server
   const server = https.createServer({
     cert: fs.readFileSync('/path/to/cert.pem'),
     key: fs.readFileSync('/path/to/key.pem')
   });
   
   const wss = new WebSocket.Server({ 
     server,
     clientTracking: true,
     perMessageDeflate: true
   });
   
   // Broadcast to all clients
   function broadcast(data) {
     wss.clients.forEach(client => {
       if (client.readyState === WebSocket.OPEN) {
         client.send(data);
       }
     });
   }
   
   // Heartbeat implementation
   function heartbeat() {
     this.isAlive = true;
   }
   
   wss.on('connection', (ws) => {
     ws.isAlive = true;
     ws.on('pong', heartbeat);
     
     // Connection handling
     ws.on('message', (message) => {
       try {
         const data = JSON.parse(message);
         handleMessage(ws, data);
       } catch (error) {
         ws.send(JSON.stringify({ error: 'Invalid message format' }));
       }
     });
   });
   
   // Cleanup dead clients
   const interval = setInterval(() => {
     wss.clients.forEach(ws => {
       if (ws.isAlive === false) return ws.terminate();
       
       ws.isAlive = false;
       ws.ping(() => {});
     });
   }, 30000);
   ```

### Client Implementation (Browser)

1. **Basic WebSocket Client**
   ```javascript
   class WebSocketClient {
     constructor(url) {
       this.url = url;
       this.reconnectAttempts = 0;
       this.maxReconnectAttempts = 5;
       this.reconnectDelay = 1000;
       this.connect();
     }
     
     connect() {
       this.ws = new WebSocket(this.url);
       
       this.ws.onopen = () => {
         console.log('Connected to server');
         this.reconnectAttempts = 0;
       };
       
       this.ws.onmessage = (event) => {
         console.log('Received:', event.data);
         this.handleMessage(event.data);
       };
       
       this.ws.onclose = () => {
         console.log('Disconnected from server');
         this.handleReconnect();
       };
       
       this.ws.onerror = (error) => {
         console.error('WebSocket error:', error);
       };
     }
     
     handleReconnect() {
       if (this.reconnectAttempts < this.maxReconnectAttempts) {
         this.reconnectAttempts++;
         setTimeout(() => this.connect(), 
           this.reconnectDelay * Math.pow(2, this.reconnectAttempts - 1));
       }
     }
     
     send(data) {
       if (this.ws.readyState === WebSocket.OPEN) {
         this.ws.send(JSON.stringify(data));
       }
     }
   }
   ```

2. **Advanced Client Features**
   ```javascript
   class EnhancedWebSocketClient extends WebSocketClient {
     constructor(url, options = {}) {
       super(url);
       this.heartbeatInterval = options.heartbeatInterval || 30000;
       this.eventHandlers = new Map();
       this.setupHeartbeat();
     }
     
     setupHeartbeat() {
       setInterval(() => {
         if (this.ws.readyState === WebSocket.OPEN) {
           this.ws.send(JSON.stringify({ type: 'ping' }));
         }
       }, this.heartbeatInterval);
     }
     
     on(event, handler) {
       if (!this.eventHandlers.has(event)) {
         this.eventHandlers.set(event, new Set());
       }
       this.eventHandlers.get(event).add(handler);
     }
     
     off(event, handler) {
       if (this.eventHandlers.has(event)) {
         this.eventHandlers.get(event).delete(handler);
       }
     }
     
     handleMessage(data) {
       try {
         const message = JSON.parse(data);
         if (this.eventHandlers.has(message.type)) {
           this.eventHandlers.get(message.type).forEach(handler => {
             handler(message.payload);
           });
         }
       } catch (error) {
         console.error('Error handling message:', error);
       }
     }
   }
   ```

## Design Considerations

### Performance Optimization

1. **Message Compression**
   ```javascript
   const WebSocket = require('ws');
   
   const wss = new WebSocket.Server({
     port: 8080,
     perMessageDeflate: {
       zlibDeflateOptions: {
         level: 6,
         memLevel: 8,
         windowBits: 15,
       },
       zlibInflateOptions: {
         windowBits: 15,
       },
       clientNoContextTakeover: true,
       serverNoContextTakeover: true,
       threshold: 1024
     }
   });
   ```

2. **Connection Pooling**
   ```javascript
   class WebSocketPool {
     constructor(maxConnections = 10) {
       this.pool = new Set();
       this.maxConnections = maxConnections;
     }
     
     addConnection(ws) {
       if (this.pool.size >= this.maxConnections) {
         const oldestConnection = this.pool.values().next().value;
         oldestConnection.close();
         this.pool.delete(oldestConnection);
       }
       this.pool.add(ws);
     }
     
     removeConnection(ws) {
       this.pool.delete(ws);
     }
   }
   ```

### Security Considerations

1. **Origin Validation**
   ```javascript
   const wss = new WebSocket.Server({
     port: 8080,
     verifyClient: (info) => {
       const origin = info.origin;
       const allowedOrigins = ['https://example.com'];
       return allowedOrigins.includes(origin);
     }
   });
   ```

2. **Authentication**
   ```javascript
   const wss = new WebSocket.Server({
     port: 8080,
     verifyClient: async (info, callback) => {
       try {
         const token = new URL(info.req.url, 'ws://localhost').searchParams.get('token');
         const isValid = await validateToken(token);
         callback(isValid, 401, 'Unauthorized');
       } catch (error) {
         callback(false, 500, 'Internal Server Error');
       }
     }
   });
   ```

## Best Practices

### Error Handling

1. **Graceful Degradation**
   ```javascript
   class WebSocketHandler {
     constructor() {
       this.fallbackToPolling = false;
       this.initializeWebSocket();
     }
     
     initializeWebSocket() {
       if ('WebSocket' in window) {
         try {
           this.ws = new WebSocket(this.url);
           this.setupWebSocket();
         } catch (error) {
           this.enablePollingFallback();
         }
       } else {
         this.enablePollingFallback();
       }
     }
     
     enablePollingFallback() {
       this.fallbackToPolling = true;
       this.initializePolling();
     }
   }
   ```

2. **Reconnection Strategy**
   ```javascript
   class ReconnectingWebSocket {
     constructor(url, options = {}) {
       this.url = url;
       this.maxRetries = options.maxRetries || 5;
       this.backoffMultiplier = options.backoffMultiplier || 1.5;
       this.initialDelay = options.initialDelay || 1000;
       this.maxDelay = options.maxDelay || 30000;
       this.currentRetry = 0;
     }
     
     calculateDelay() {
       const delay = this.initialDelay * 
         Math.pow(this.backoffMultiplier, this.currentRetry);
       return Math.min(delay, this.maxDelay);
     }
     
     reconnect() {
       if (this.currentRetry < this.maxRetries) {
         this.currentRetry++;
         const delay = this.calculateDelay();
         setTimeout(() => this.connect(), delay);
       }
     }
   }
   ```

## Interview Tips
- Understand WebSocket protocol
- Know handshake process
- Explain frame format
- Understand security implications
- Be familiar with:
  - Connection lifecycle
  - Error handling
  - Performance optimization
  - Security measures
- Real-world scenarios:
  - Chat applications
  - Real-time dashboards
  - Live notifications
  - Collaborative tools
- Best practices:
  - Connection management
  - Error handling
  - Security implementation
  - Performance tuning
- Advanced concepts:
  - Subprotocols
  - Extensions
  - Load balancing
  - Scaling strategies 