## React 实现

### 概要

• **WebSocketProvider + Context API**：适用于需要在多个组件中共享 WebSocket 状态和消息的场景。通过 Context API 提供全局的 WebSocket 状态，使得 WebSocket 连接和消息处理逻辑更加集中化、模块化。

• **自定义 Hook**：适用于单独使用 WebSocket 连接的组件。简单封装了 WebSocket 的所有逻辑，便于复用。

这两种方法都可以根据需求来封装 WebSocket 逻辑，使得应用在使用 WebSocket 时更加结构化、易于维护。

### 1. 使用 React Context API 封装 WebSocket

通过 Context API，我们可以将 WebSocket 连接、状态和消息推送逻辑封装成一个全局可用的 context，并在子组件中消费该 context。

**步骤：**

1. 创建 WebSocket Context 和提供者。
2. 在子组件中消费 WebSocket 状态。

```TypeScript
import React, { createContext, useContext, useEffect, useState } from 'react';

// WebSocket context
const WebSocketContext = createContext(null);

// WebSocket Provider
export const WebSocketProvider = ({ children }) => {
  const [messages, setMessages] = useState([]);
  const [socket, setSocket] = useState(null);

  // Initialize WebSocket connection
  useEffect(() => {
    const ws = new WebSocket('wss://your-websocket-server.com'); // 替换为你的 WebSocket 服务器地址

    ws.onopen = () => {
      console.log('WebSocket connection established');
    };

    ws.onmessage = (event) => {
      const message = event.data;
      setMessages((prevMessages) => [...prevMessages, message]);
    };

    ws.onclose = () => {
      console.log('WebSocket connection closed');
    };

    ws.onerror = (error) => {
      console.error('WebSocket Error:', error);
    };

    setSocket(ws);

    return () => {
      if (ws) {
        ws.close();
      }
    };
  }, []);

  // Function to send message to WebSocket server
  const sendMessage = (msg) => {
    if (socket && msg) {
      socket.send(msg);
    }
  };

  return (
    <WebSocketContext.Provider value={{ messages, sendMessage }}>
      {children}
    </WebSocketContext.Provider>
  );
};

// Custom hook to use WebSocket context
export const useWebSocket = () => {
  return useContext(WebSocketContext);
};
```

- [ ] **1. 创建 WebSocket Context 和提供者**

```TypeScript
import React, { useState } from 'react';
import { useWebSocket } from './WebSocketContext';

const WebSocketComponent = () => {
  const { messages, sendMessage } = useWebSocket();
  const [newMessage, setNewMessage] = useState('');

  const handleSendMessage = () => {
    sendMessage(newMessage);
    setNewMessage('');
  };

  return (
    <div>
      <h1>WebSocket Demo</h1>
      <div>
        <h3>Messages:</h3>
        <ul>
          {messages.map((msg, index) => (
            <li key={index}>{msg}</li>
          ))}
        </ul>
      </div>
      <div>
        <input
          type="text"
          value={newMessage}
          onChange={(e) => setNewMessage(e.target.value)}
          placeholder="Type a message"
        />
        <button onClick={handleSendMessage}>Send</button>
      </div>
    </div>
  );
};

export default WebSocketComponent;
```

- [ ] **2. 在应用中使用 WebSocket Context**

```TypeScript
import React from 'react';
import ReactDOM from 'react-dom';
import { WebSocketProvider } from './WebSocketContext';
import WebSocketComponent from './WebSocketComponent';

const App = () => {
  return (
    <WebSocketProvider>
      <WebSocketComponent />
    </WebSocketProvider>
  );
};

ReactDOM.render(<App />, document.getElementById('root'));
```

- [ ] **3. 在应用中包裹 WebSocketProvider**

```TypeScript
import React from 'react';
import ReactDOM from 'react-dom';
import { WebSocketProvider } from './WebSocketContext';
import WebSocketComponent from './WebSocketComponent';

const App = () => {
  return (
    <WebSocketProvider>
      <WebSocketComponent />
    </WebSocketProvider>
  );
};

ReactDOM.render(<App />, document.getElementById('root'));
```

### 2. 使用 自定义 Hook 封装 WebSocket

如果你更喜欢不使用 Context API，而是通过独立的自定义 Hook 来封装 WebSocket 逻辑，可以这样做：

```TypeScript
import { useState, useEffect } from 'react';

// WebSocket custom hook

const useWebSocket = (url) => {

  const [messages, setMessages] = useState([]);

  const [socket, setSocket] = useState(null);

  // Initialize WebSocket connection

  useEffect(() => {

  const ws = new WebSocket(url);

  ws.onopen = () => {

  console.log('WebSocket connection established');

  };

  ws.onmessage = (event) => {

  const message = event.data;

  setMessages((prevMessages) => [...prevMessages, message]);

  };

  ws.onclose = () => {

  console.log('WebSocket connection closed');

  };

  ws.onerror = (error) => {

  console.error('WebSocket Error:', error);

  };

  setSocket(ws);

  return () => {

  if (ws) {

  ws.close();

  }

  };

  }, [url]);

  // Function to send message to WebSocket server

  const sendMessage = (msg) => {

  if (socket && msg) {

  socket.send(msg);

  }

  };

  return { messages, sendMessage };

};

export default useWebSocket;
```

#### 在组件中使用自定义 Hook：

```TypeScript
import React, { useState } from 'react';

import useWebSocket from './useWebSocket';

const WebSocketComponent = () => {

  const [newMessage, setNewMessage] = useState('');

  const { messages, sendMessage } = useWebSocket('wss://your-websocket-server.com');

  const handleSendMessage = () => {

  sendMessage(newMessage);

  setNewMessage('');

  };

  return (

  <div>

  <h1>WebSocket Demo</h1>

  <div>

  <h3>Messages:</h3>

  <ul>

  {messages.map((msg, index) => (

  <li key={index}>{msg}</li>

  ))}

  </ul>

  </div>

  <div>

  <input

  type="text"

  value={newMessage}

  onChange={(e) => setNewMessage(e.target.value)}

  placeholder="Type a message"

  />

  <button onClick={handleSendMessage}>Send</button>

  </div>

  </div>

  );

};

export default WebSocketComponent;
```

## Vue 实现

### 插件式封装

#### 1. 封装 WebSocket 插件

```ts
// websocket-plugin.ts
export default {
  install(app, { url }) {
    const socket = new WebSocket(url);

    // 全局 WebSocket 状态
    const state = {
      messages: [],
      isConnected: false,
    };

    socket.onopen = () => {
      state.isConnected = true;
      console.log("WebSocket connection established.");
    };

    socket.onmessage = (event) => {
      state.messages.push(event.data);
    };

    socket.onclose = () => {
      state.isConnected = false;
      console.log("WebSocket connection closed.");
    };

    socket.onerror = (error) => {
      console.error("WebSocket error:", error);
    };

    app.config.globalProperties.$websocket = {
      sendMessage(message) {
        if (state.isConnected) {
          socket.send(message);
        } else {
          console.error("WebSocket is not connected.");
        }
      },
      state,
    };
  },
};
```

#### 2. 在 Vue 中注册插件

```ts
// main.ts
import { createApp } from "vue";
import App from "./App.vue";
import WebSocketPlugin from "./websocket-plugin";

const app = createApp(App);
app.use(WebSocketPlugin, { url: "wss://your-websocket-server.com" });
app.mount("#app");
```

#### 3. 组件中使用 WebSocket

```vue
<template>
  <div>
    <h1>WebSocket Messages</h1>
    <ul>
      <li v-for="(msg, index) in $websocket.state.messages" :key="index">
        {{ msg }}
      </li>
    </ul>
    <input v-model="newMessage" placeholder="Type a message" />
    <button @click="sendMessage">Send</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newMessage: "",
    };
  },
  methods: {
    sendMessage() {
      this.$websocket.sendMessage(this.newMessage);
      this.newMessage = "";
    },
  },
};
</script>
```

### 组合式 API 封装

#### 1. 封装 WebSocket 的自定义 Hook

```ts
// useWebSocket.ts
import { ref, onMounted, onUnmounted } from "vue";

export function useWebSocket(url: string) {
  const messages = ref<string[]>([]);
  const socket = ref<WebSocket | null>(null);
  const isConnected = ref(false);

  onMounted(() => {
    const ws = new WebSocket(url);

    ws.onopen = () => {
      isConnected.value = true;
      console.log("WebSocket connection established.");
    };

    ws.onmessage = (event) => {
      messages.value.push(event.data);
    };

    ws.onclose = () => {
      isConnected.value = false;
      console.log("WebSocket connection closed.");
    };

    ws.onerror = (error) => {
      console.error("WebSocket error:", error);
    };

    socket.value = ws;
  });

  onUnmounted(() => {
    if (socket.value) {
      socket.value.close();
    }
  });

  const sendMessage = (message: string) => {
    if (socket.value && isConnected.value) {
      socket.value.send(message);
    } else {
      console.error("WebSocket is not connected.");
    }
  };

  return {
    messages,
    isConnected,
    sendMessage,
  };
}
```

#### 2. 组件中使用自定义 Hook

```vue
<template>
  <div>
    <h1>WebSocket Messages</h1>
    <ul>
      <li v-for="(msg, index) in messages" :key="index">{{ msg }}</li>
    </ul>
    <input v-model="newMessage" placeholder="Type a message" />
    <button @click="sendMessage">Send</button>
  </div>
</template>

<script>
import { ref } from "vue";
import { useWebSocket } from "./useWebSocket";

export default {
  setup() {
    const { messages, sendMessage } = useWebSocket(
      "wss://your-websocket-server.com",
    );
    const newMessage = ref("");

    const handleSendMessage = () => {
      sendMessage(newMessage.value);
      newMessage.value = "";
    };

    return {
      messages,
      newMessage,
      sendMessage: handleSendMessage,
    };
  },
};
</script>
```
