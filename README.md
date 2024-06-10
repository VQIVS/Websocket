# WebSocket Programming in Go (Golang)

## Introduction

WebSockets are a protocol that provides full-duplex communication channels over a single TCP connection. This means that data can flow in both directions simultaneously, enabling real-time communication between a client and a server. WebSockets are ideal for applications like chat apps, live notifications, and online gaming.

## Key Features of WebSockets

- **Full-Duplex Communication**: Unlike HTTP, which is request-response based, WebSockets allow both the client and server to send and receive messages independently.
- **Persistent Connection**: Once a WebSocket connection is established, it remains open until either the client or server closes it. This reduces the overhead associated with establishing multiple HTTP connections.
- **Real-Time Data**: WebSockets are perfect for applications that require real-time updates, such as chat applications, live sports updates, online gaming, and financial tickers.

## WebSockets in Go (Golang)

Go, also known as Golang, is a statically typed, compiled programming language designed at Google. It is known for its simplicity, concurrency support, and efficiency. Go has strong support for WebSockets, primarily through third-party libraries like `gorilla/websocket`.

## Structure of the Code

The sample WebSocket server code in Go is structured as follows:

### 1. Importing Packages

```go
import (
    "fmt"
    "log"
    "net/http"
    "github.com/gorilla/websocket"
)
```
- **fmt**: Provides functions for formatting strings.\n
- **log**: Provides simple logging capabilities.
- **net/http**: Implements HTTP client and server.
- **github.com/gorilla/websocket**: A third-party package that provides WebSocket support for Go.
### 2. Defining the Upgrader
```go

var upgrader = websocket.Upgrader{
    CheckOrigin: func(r *http.Request) bool {
        return true
    },
}
```
- **websocket.Upgrader**: Used to upgrade an HTTP connection to a WebSocket connection.
- The CheckOrigin function allows all connections for simplicity. In a real application, you should implement proper origin checks to enhance security.

### 3. Handling Connections
```go
func handleConnections(w http.ResponseWriter, r *http.Request) {
    ws, err := upgrader.Upgrade(w, r, nil)
    if err != nil {
        log.Fatalf("Error upgrading connection: %v", err)
    }
    defer ws.Close()

    for {
        var msg map[string]string
        err := ws.ReadJSON(&msg)
        if err != nil {
            log.Printf("Error reading json: %v", err)
            break
        }
        fmt.Printf("Received: %v\n", msg)
        msg["reply"] = "Message received"
        err = ws.WriteJSON(msg)
        if err != nil {
            log.Printf("Error writing json: %v", err)
            break
        }
    }
}
```
- **handleConnections**: Function to handle incoming WebSocket connections.
- ws, err := upgrader.Upgrade(w, r, nil): Upgrades the HTTP connection to a WebSocket.
- defer ws.Close(): Ensures the WebSocket is closed when the function exits.
- A for loop reads messages from the WebSocket, prints them to the console, and sends a response back to the client.
- ws.ReadJSON(&msg): Reads a JSON message from the WebSocket.
- fmt.Printf("Received: %v\n", msg): Prints the received message to the console.
- msg["reply"] = "Message received": Adds a reply to the message.
- ws.WriteJSON(msg): Writes the JSON message back to the client.
### 4. Main Function
```go
func main() {
    http.HandleFunc("/ws", handleConnections)
    log.Println("Server started on :8080")
    err := http.ListenAndServe(":8080", nil)
    if err != nil {
        log.Fatalf("Error starting server: %v", err)
    }
}
```
- **main**: The entry point of the application.
- http.HandleFunc("/ws", handleConnections): Registers the handler for WebSocket connections at the /ws endpoint.
- log.Println("Server started on :8080"): Logs that the server is starting.
- http.ListenAndServe(":8080", nil): Starts the HTTP server on port 8080 and listens for incoming connections.


## Running the Server
```shell
go run main.go
```

## Sample WebSocket Client (Optional)
```html
<!DOCTYPE html>
<html>
<head>
    <title>WebSocket Test</title>
</head>
<body>
    <script type="text/javascript">
        const ws = new WebSocket('ws://localhost:8080/ws');

        ws.onopen = function() {
            console.log('WebSocket connection established');
            ws.send(JSON.stringify({ message: 'Hello Server' }));
        };

        ws.onmessage = function(event) {
            const msg = JSON.parse(event.data);
            console.log('Message from server:', msg);
        };

        ws.onclose = function() {
            console.log('WebSocket connection closed');
        };
    </script>
</body>
</html>
```
###### Open this file in your web browser to test the WebSocket connection.


## Conclusion
- WebSockets provide full-duplex, real-time communication, making them ideal for applications like chat and live notifications.
- The Go programming language, using the gorilla/websocket package, offers strong support for WebSocket implementation.



This `README.md` file now includes properly formatted explanations and code snippets with appropriate line breaks, ensuring clarity and readability.
