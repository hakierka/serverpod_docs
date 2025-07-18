---
sidebar_position: -1
sidebar_label: 🏠 Home
---

# 👋 Welcome to Serverpod Docs
---

Serverpod is the backend framework we always wanted: zero boilerplate, fully typed, and 100% Dart. Built for Flutter developers who want more control and less complexity.

- 🧠 **Code-first API**: Define endpoints in Dart and call them like local methods in Flutter
- 🗄️ **PostgreSQL built-in**: Dart-native ORM with type-safe queries and schema migrations
- 🔐 **Auth that just works**: Email/password, Google, Apple, Firebase — ready to go
- 📡 **Real-time communication**: WebSockets via Dart streams, zero config
- 🧰 **Full control**: No magic. No lock-in. Deploy anywhere, extend everything


## 🚀 Where to start?

| 🧪 I want to start building with Serverpod      | 📚 I want to learn how to use Serverpod |
| ----------- | ----------- |
| 👉 [Get Started](./get-started)     | 👉 [Explore Tutorials](./tutorials)       |

---

### 🎥 Watch: What is Serverpod?

A quick visual overview of what Serverpod is and how it helps Dart and Flutter developers build backends fast.

<div style={{ position : 'relative', paddingBottom : '56.25%', height : '0' }}><iframe style={{ position : 'absolute', top : '0', left : '0', width : '100%', height : '100%' }} width="560" height="315" src="https://www.youtube-nocookie.com/embed/teOnBD5d8b8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

## 🧭 Overview

Serverpod is how you scale your Dart backend without duct tape. It includes logging, auth, file uploads, database migrations, and background tasks — all in one codebase. Designed for teams that care about performance and maintainability.

### Key capabilities:
- **Full-stack Dart**: Same language, client to server
- **Automatic code generation** for models and APIs
- **Relational ORM** that feels like Dart, not SQL
- **Dev-first logging and error insights**
- **Streamed responses with built-in WebSocket handling**

---
## 🔧 Serverpod in action

Write Dart. That’s it. No boilerplate, no codegen gymnastics. Just you, your logic, and a typed API that’s always in sync.

```dart
// Server: define a public API
class ExampleEndpoint extends Endpoint {
  Future<String> greet(Session session, String name) async {
    return 'Hello, $name';
  }
}
```

```dart
// Client: call the endpoint like a normal function
final greeting = await client.example.greet('World');
print(greeting); // Hello, World
```

### 🌱 Progressive by design

- Start small — generate only what you need
- Scale fast — structure apps with modules, tasks, queues, and migrations
- Connect everything — use Redis, Docker, GCP, AWS, or your own Linux box

### 🛠️ Developer-focused features

- Type-safe ORM and query builder
- Real-time streams with zero WebSocket boilerplate
- Background tasks (schedule in the future or trigger async)
- Native support for file uploads and cloud storage

> 💡 Start building now → [Get Started](./get-started)
