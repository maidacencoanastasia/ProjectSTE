# ProjectSTE — Python Networking Projects

A collection of two Python socket-based network applications:

1. **Multi-Client Chat Room** — a broadcast TCP chat server
2. **Networked Tic-Tac-Toe** — a single-round player-vs-computer game over TCP

---

## Project Structure

```
ProjectSTE/
├── chat_server.py   # Multi-client TCP chat server
├── server.py        # Tic-Tac-Toe TCP server (computer/O player)
├── client.py        # Tic-Tac-Toe TCP client (human/X player)
├── main.py          # Standalone local Tic-Tac-Toe board demo
└── README.md
```

---

## Requirements

- Python 3.x (no third-party libraries required — uses standard library only)

---

## 1. Multi-Client Chat Room

### How it works

`chat_server.py` is a TCP server that accepts up to 100 simultaneous client connections. Each connected client gets its own thread. When a client sends a message, the server **broadcasts** it to every other connected client, prefixing it with the sender's IP address.

### Running the chat server

```bash
python chat_server.py <IP_ADDRESS> <PORT>
```

**Example:**
```bash
python chat_server.py 127.0.0.1 9090
```

### Connecting clients

Any TCP client can connect (e.g., `telnet`, `netcat`, or a custom Python socket client):

```bash
# Using netcat
nc 127.0.0.1 9090

# Using telnet
telnet 127.0.0.1 9090
```

Once connected, each client receives a welcome message and can send text that will be broadcast to all other connected users.

### Notes

- The IP address and port **must** be passed as command-line arguments; the script will exit with a usage message otherwise.
- The server uses `SO_REUSEADDR` so the port can be reused immediately after restart.
- Messages are displayed on the server terminal in the format: `<client_ip> message`

---

## 2. Networked Tic-Tac-Toe

### How it works

The game is split across two scripts:

| File | Role | Symbol |
|------|------|--------|
| `client.py` | Human player | **X** |
| `server.py` | Computer (random move) | **O** |

**Game flow (one round):**
1. The client displays a numbered 3×3 board (positions 1–9).
2. The human picks a cell number; `X` is placed on that cell.
3. The entire board state is sent to the server as a space-separated string.
4. The server picks a random available cell and places `O`.
5. The updated board is sent back to the client and displayed.
6. The connection closes (one exchange per connection).

> **Note:** The current implementation handles a single move per connection. Win/loss detection and a full game loop are not yet implemented.

### Configuration

Both `server.py` and `client.py` are hardcoded to:

```python
HOST = '172.31.96.1'
PORT = 8080
```

Change these values in both files to match your network setup before running.

### Running the Tic-Tac-Toe game

**Step 1 — Start the server first:**
```bash
python server.py
```

**Step 2 — Start the client in a separate terminal:**
```bash
python client.py
```

**Example session (client terminal):**
```
Welcome to tic-Tac-Toe
You make your first move
Current Board:
-------------
1 ║ 2 ║ 3
══╬═══╬══
4 ║ 5 ║ 6
══╬═══╬══
7 ║ 8 ║ 9
-------------
Enter the number of cell: 5
```

---

## 3. Local Board Demo (`main.py`)

`main.py` is a self-contained, offline script that displays the Tic-Tac-Toe board and processes a single `X` move locally — no networking involved. Use it to quickly test or demonstrate the board rendering logic.

```bash
python main.py
```

---

## Known Limitations / Potential Improvements

- **Chat server:** Messages are sent and received as raw bytes with no encoding specified — adding `.encode('utf-8')` / `.decode('utf-8')` would improve compatibility.
- **Tic-Tac-Toe:** 
  - Win/draw detection is not implemented.
  - Only one move is exchanged per TCP connection; a full game loop would require keeping the connection open across turns.
  - The host/port are hardcoded; passing them as arguments (like `chat_server.py` does) would make the app more flexible.
  - The computer currently plays randomly; a smarter AI (e.g., minimax) could be added.
