Colyseus is a multiplayer game server framework for Node.js that simplifies real-time multiplayer game development. Below is a step-by-step **Colyseus tutorial** to help you get started. 🚀  

---

## **1️⃣ Install and Set Up Colyseus**
### **1.1 Create a New Colyseus Project**
Run the following command to create a new Colyseus server project:
```sh
npm create colyseus-app@latest my-server
```
This will create a new project in the `my-server` folder.

### **1.2 Install Dependencies**
Navigate into the project and install dependencies:
```sh
cd my-server
npm install
```

### **1.3 Start the Server**
Run the server:
```sh
npm start
```
By default, Colyseus runs on `ws://localhost:2567`.

---

## **2️⃣ Understanding the Project Structure**
Your Colyseus server will have a structure like this:

```
my-server/
│── src/
│   ├── index.ts      # Main server entry point
│   ├── rooms/
│   │   ├── MyRoom.ts # Example game room
│── package.json
│── tsconfig.json     # TypeScript settings
│── README.md
```

- `index.ts`: Initializes the Colyseus server and defines routes.
- `rooms/MyRoom.ts`: Defines a game room where players can join and interact.

---

## **3️⃣ Create a Simple Game Room**
Modify `src/rooms/MyRoom.ts` to define a basic multiplayer room:

### **3.1 Define Game Room State**
Edit `MyRoom.ts`:
```typescript
import { Room, Client } from "colyseus";

export class MyRoom extends Room {
    maxClients = 4; // Max 4 players in a room

    onCreate() {
        console.log("MyRoom created!");
    }

    onJoin(client: Client) {
        console.log(client.sessionId, "joined the room");
    }

    onLeave(client: Client) {
        console.log(client.sessionId, "left the room");
    }

    onMessage(client: Client, message: any) {
        console.log(client.sessionId, "sent message:", message);
    }
}
```
- `onCreate()`: Called when the room is created.
- `onJoin(client)`: Called when a player joins.
- `onLeave(client)`: Called when a player leaves.
- `onMessage(client, message)`: Handles messages from players.

---

## **4️⃣ Register the Room in `index.ts`**
Open `src/index.ts` and register the room:

```typescript
import { Server } from "colyseus";
import { createServer } from "http";
import { MyRoom } from "./rooms/MyRoom";

const port = 2567;
const httpServer = createServer();
const gameServer = new Server({ server: httpServer });

gameServer.define("my_room", MyRoom);

httpServer.listen(port, () => console.log(`Listening on ws://localhost:${port}`));
```
Now, `my_room` is available for clients to join.

---

## **5️⃣ Connect a Client**
### **5.1 Install Colyseus Client**
```sh
npm install colyseus.js
```

### **5.2 Create a Simple Client**
Create a new file `client.js`:
```javascript
const colyseus = require("colyseus.js");

const client = new colyseus.Client("ws://localhost:2567");

async function joinGame() {
    const room = await client.joinOrCreate("my_room");
    console.log("Joined room with session ID:", room.sessionId);

    room.onMessage((message) => {
        console.log("Received:", message);
    });

    // Send a message after 3 seconds
    setTimeout(() => {
        room.send({ action: "hello" });
    }, 3000);
}

joinGame();
```

Run the client:
```sh
node client.js
```

Now, when the client joins, you should see logs on the server.

---

## **6️⃣ Handling Player State**
Modify `MyRoom.ts` to track players:

```typescript
import { Room, Client } from "colyseus";

interface Player {
    x: number;
    y: number;
}

export class MyRoom extends Room<{ players: { [id: string]: Player } }> {
    onCreate() {
        this.setState({ players: {} });
    }

    onJoin(client: Client) {
        this.state.players[client.sessionId] = { x: 0, y: 0 };
    }

    onMessage(client: Client, message: any) {
        if (message.action === "move") {
            this.state.players[client.sessionId].x += message.x;
            this.state.players[client.sessionId].y += message.y;
        }
    }

    onLeave(client: Client) {
        delete this.state.players[client.sessionId];
    }
}
```
This allows players to move within the game.

---

## **7️⃣ Deploying the Server**
Once your game server is ready, deploy it using **Docker**:
```sh
docker build -t my-colyseus-server .
docker run -p 2567:2567 my-colyseus-server
```
Or deploy to **Heroku, AWS, or DigitalOcean**.

---

## **8️⃣ Next Steps**
- Implement game logic (e.g., physics, scoring).
- Add front-end (React, Phaser.js, or Unity).
- Secure the server with authentication.

---

### **🎮 Summary**
✅ Set up a Colyseus server  
✅ Created a simple multiplayer room  
✅ Connected a client  
✅ Implemented player movement  
✅ Learned about deployment  

Now you can build your **own real-time multiplayer game** using Colyseus! 🚀 Let me know if you need more details. 😊