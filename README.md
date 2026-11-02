![Flutter](https://img.shields.io/badge/Flutter-%2302569B.svg?style=for-the-badge&logo=Flutter&logoColor=white)
![Dart](https://img.shields.io/badge/Dart-%230175C2.svg?style=for-the-badge&logo=dart&logoColor=white)
![Android](https://img.shields.io/badge/Android-%233DDC84.svg?style=for-the-badge&logo=android&logoColor=white)
![iOS](https://img.shields.io/badge/iOS-%23000000.svg?style=for-the-badge&logo=ios&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-%23FFCA28.svg?style=for-the-badge&logo=firebase&logoColor=black)
![Neon](https://img.shields.io/badge/Neon-%2300E599.svg?style=for-the-badge&logo=neon&logoColor=black)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-%23339933.svg?style=for-the-badge&logo=nodedotjs&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-%234169E1.svg?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-%23DD0031.svg?style=for-the-badge&logo=redis&logoColor=white)
![Git](https://img.shields.io/badge/git-%23F05033.svg?style=for-the-badge&logo=git&logoColor=white)

# Restaurant Proximity Service <img src="icon.png" width="30px" alt="icon">

A mobile app to search for restaurants in an easy and personalized way, with an integrated agent for natural language interaction.

<br>

## Description

Built on a serverless architecture with a dual-database model, Firebase Auth handles user data, while Neon Serverless PostgreSQL manages the restaurant catalog, PostGIS spatial data, and the agent's semantic long-term memory. 

The backend relies on AWS Lambda (Node.js) routed through AWS API Gateway for Firebase JWT validation and response streaming. ElastiCache Redis powers the in-memory layer for 24-hour TTL ephemeral chat context and geospatial query caching. The mobile client is developed with Flutter and Dart, using Hive for local persistence.

<br>

## Table of Contents

- [Restaurant Proximity Service ](#restaurant-proximity-service-)
  - [Description](#description)
  - [Table of Contents](#table-of-contents)
  - [1- Properties](#1--properties)
    - [1.1- Key Features](#11--key-features)
    - [1.2- Interface](#12--interface)
      - [Views](#views)
      - [Screenshots](#screenshots)
    - [1.3- Requirements](#13--requirements)
      - [Functional Requirements](#functional-requirements)
      - [Non-Functional Requirements](#non-functional-requirements)
  - [2- Architecture](#2--architecture)
    - [2.1- Diagram](#21--diagram)
      - [Architecture Overview](#architecture-overview)
      - [Agent Chat Flow](#agent-chat-flow)
      - [Authentication Flow](#authentication-flow)
      - [Nearby Search Flow](#nearby-search-flow)
    - [2.2- System Performance \& Scalability Design](#22--system-performance--scalability-design)
      - [Client \& Caching Strategy](#client--caching-strategy)
      - [Infrastructure \& API Layer](#infrastructure--api-layer)
      - [User Data Layer (Firebase)](#user-data-layer-firebase)
      - [Database \& Storage Layer (Neon PostgreSQL)](#database--storage-layer-neon-postgresql)
      - [Serverless Compute (AWS Lambda)](#serverless-compute-aws-lambda)
      - [AI Agent Memory Architecture](#ai-agent-memory-architecture)
    - [2.3- Tech Stack](#23--tech-stack)
      - [Tools](#tools)
      - [Packages](#packages)
      - [Standardization](#standardization)
    - [2.4- Folder Structure (Feature-First)](#24--folder-structure-feature-first)
      - [Frontend/](#frontend)
      - [Backend/](#backend)
    - [2.5- Data Model](#25--data-model)
      - [Cloud Firestore (User Data)](#cloud-firestore-user-data)
      - [Neon PostgreSQL (Catalog \& Agent)](#neon-postgresql-catalog--agent)
      - [Redis (Ephemeral)](#redis-ephemeral)
    - [2.6- API Reference](#26--api-reference)
      - [Backend REST API (Lambda)](#backend-rest-api-lambda)
      - [Firebase SDK (Client)](#firebase-sdk-client)
  - [3- Development and Execution Guide](#3--development-and-execution-guide)
    - [3.1- Setup Environment](#31--setup-environment)
      - [Pre-Requisites](#pre-requisites)
    - [3.2 Local Execution](#32-local-execution)
      - [Frontend](#frontend-1)
      - [Backend](#backend-1)
    - [3.3- Environment Variables](#33--environment-variables)
    - [3.4- CI/CD and Deployment](#34--cicd-and-deployment)

<br>

## 1- Properties

### 1.1- Key Features

- **Discover | Home:** To receive recommendations of restaurants
- **Map | Explore:** Interactive map for realtime geospatial exploration
- **Filters:** To find places on the map with specific properties
- **Chat LLM Agent:** To attend requests in natural language
- **Save Places | Favorites:** To bookmark special locations and details

### 1.2- Interface

#### Views

- **Home:** Home page with recomendations of the day, promotions and places by preference
- **Explore:** Page with an interactive map featuring search and filter options
- **Filters:** Personalized settings to filter restaurants
- **Chat:** Direct chat with the agent for natural language and setup preferences
- **Favorites:** Saved restaurants with cards for restaurants with their details
- **Settings:** Parameters of account, dark theme, change of email or password, logout

#### Screenshots

|     Home     |      Explore    |   Filters  |   Chat   |
| :----------: | :----------: | :----------: | :----------: |
|<img src="https://via.placeholder.com/300x600?text=Home" height="450" />|<img src="https://via.placeholder.com/300x600?text=Explore" height="450" />|<img src="https://via.placeholder.com/300x600?text=Filters" height="450" />|<img src="https://via.placeholder.com/300x600?text=Chat" height="450" />|

|    Favorites   |   Settings   |  Dark Mode   | 
| :----------: | :----------: | :----------: |
|<img src="https://via.placeholder.com/300x600?text=Favorites" height="450" />|<img src="https://via.placeholder.com/300x600?text=Settings" height="450" />|<img src="https://via.placeholder.com/300x600?text=DarkMode" height="450" />|

### 1.3- Requirements

#### Functional Requirements

- **FR-01:** The system must allow users to explore nearby restaurants on an interactive map using real-time geolocation, and to open a location details sheet with rating, description and images.
- **FR-02:** The system must allow users to search and filter restaurants by labels, radius, time of arrival, properties, price range and natural language.
- **FR-03:** The system must provide a LLM agent capable of processing natural language, queries and user preference configurations, and able to return restaurant cards inside the chat.
- **FR-04:** The agent must store user preferences as vectorized parameters in Neon (`pgvector`) and retrieve them per user to deliver personalized responses.
- **FR-05:** The agent must manage the immediate conversational context with a 24-hour TTL ephemeral memory using Redis.
- **FR-06:** The agent must store every conversation permanently in Neon, scoped to the authenticated user, allowing each user to list, resume and delete their own conversations.
- **FR-07:** The system must allow users to save, view, and remove restaurants from their personal favorites list, stored per user in Firebase.
- **FR-08:** The system must display on Home the recommendations of the day, promotions and places by user preference in sections with carousels.
- **FR-09:** The system must allow users to sign up, sign in and sign out with Firebase Authentication.
- **FR-10:** The system must allow users to manage account parameters (change name, email and password), delete their account with all their data, and toggle between dark and light UI themes.
- **FR-11:** The system must allow users to enable or disable notifications (personalized recommendations and alerts for restaurants) and reports from Settings.
- **FR-12:** The application must cache recommendations, active filters, theme and recent chat messages locally via Hive to accelerate UI rendering, while Firestore offline persistence keeps favorites and settings available.
- **FR-13:** The serverless API layer must handle exceptions asynchronously using standard HTTP response wrappers to maintain API gateway stability.

#### Non-Functional Requirements

- **NFR-01: Performance:** The Flutter mobile client must sustain a render speed of 60 FPS during map panning, screen transitions, and navigation.
- **NFR-02: Latency:** The geospatial endpoints must deliver API responses with sub-second latencies using Redis caching, and the LLM conversational agent must deliver the first token in under one second using response streaming.
- **NFR-03: Scalability:** The backend infrastructure must scale dynamically using AWS Lambda, Neon Serverless Database and Firebase under burst connection loads.
- **NFR-04: Optimization:** The application must have hybrid memory management for faster responses, permanent chats in Neon, immediate memory in Redis with TTL and vectorized parameters in Neon for persistent agent memory.
- **NFR-05: Compatibility:** The mobile application must natively support both iOS and Android platforms.
- **NFR-06: Security:** All communications must be encrypted in transit via HTTPS through AWS API Gateway, with JWT using Firebase validated at the gateway, with every Neon and Redis record scoped by the Firebase UID.
- **NFR-07: Privacy:** Deleting an account must erase the user's data in Firebase (Authentication and Firestore), Neon (conversations, messages and memories), Redis and the local Hive cache.

<br>

## 2- Architecture

### 2.1- Diagram

#### Architecture Overview

```mermaid
flowchart LR
    subgraph Frontend["Mobile App (Flutter) / Frontend"]
        UI["UI + flutter_bloc"]
        Hive[("Hive<br/>Local Cache")]
    end

    subgraph Backend["API / Backend"]
        subgraph APIGWBox[" "]
            APIGW["AWS API Gateway (HTTP API)<br/>JWT Authorizer + Throttling"]
        end

        subgraph Compute["AWS Lambda (Node.js, arm64)"]
            RestFn["restaurants"]
            DiscFn["discover"]
            AccFn["account"]
            AgentFn["agent<br/>(Response Streaming)"]
            Orch["orchestrator<br/>Function Calling"]
        end

        subgraph Async["Async Processing"]
            Sched["EventBridge Scheduler<br/>Reports"]
            SQS["AWS SQS"]
            Worker["Lambda Worker<br/>Notifications + Reports"]
        end

        LLM["LLM Providers<br/>Gemini / Groq"]
    end

    subgraph Databases["Memory Layer / Databases"]
        direction TB
        
        subgraph Firebase["Firebase (User Data)"]
            FAuth["Authentication"]
            FStore[("Cloud Firestore<br/>Profile, Settings, Favorites")]
            FCM["Cloud Messaging"]
        end
        
        subgraph Agent["Agents Memory"]
            Redis[("ElastiCache Redis<br/>Cache + 24h Chat TTL")]
            Neon[("Neon PostgreSQL<br/>PostGIS + pgvector<br/>Catalog, Chats, Memory")]
        end
    end

    %% Frontend
    UI <--> Hive
    UI <-- "Login, profile,<br/>favorites, settings" --> FAuth
    UI <--> FStore
    UI -- "HTTPS + Firebase JWT" --> APIGW
    FCM -.-> UI

    %% API Gateway
    APIGW -. "validate JWT (JWKS)" .-> FAuth
    APIGW --> RestFn
    APIGW --> DiscFn
    APIGW --> AgentFn
    APIGW --> AccFn
    AgentFn --> Orch

    %% Lambdas to memory layer
    RestFn <--> Redis
    RestFn <--> Neon
    DiscFn <--> Redis
    DiscFn <--> Neon
    Orch <--> Redis
    Orch <--> Neon
    Orch -. "read favorites<br/>(firebase-admin)" .-> FStore
    AccFn --> Neon
    AccFn --> Redis
    AccFn -- "delete user data" --> FStore
    AccFn -- "delete user" --> FAuth

    %% LLM and async processing
    Orch -- "Keep-Alive" --> LLM
    Orch --> SQS
    Sched --> SQS
    SQS --> Worker
    Worker -. "tokens + toggles" .-> FStore
    Worker -- "Push" --> FCM

    style Frontend fill:transparent,stroke:#4a7bd0,stroke-width:2px

    style Backend fill:transparent,stroke:#e0902b,stroke-width:2px
    style Compute fill:transparent,stroke:#e0902b,stroke-width:2px
    style Async fill:transparent,stroke:#e0902b,stroke-width:2px
    style APIGWBox fill:transparent,stroke:#e0902b,stroke-width:2px

    style Databases fill:transparent,stroke:#3a9d5d,stroke-width:2px
    style Agent fill:transparent,stroke:#3a9d5d,stroke-width:2px
    style Firebase fill:transparent,stroke:#3a9d5d,stroke-width:2px
```


#### Agent Chat Flow

```mermaid
flowchart LR
    U["User sends a message in Chat<br/>(Firebase JWT)"] --> GW["API Gateway<br/>JWT Authorizer"]
    GW --> A["Lambda agent<br/>uid from JWT claims"]
    A --> O["Orchestrator"]
    O --> RC{"Context in Redis?<br/>chat:uid:conversation"}
    RC -- "Yes" --> CTX["Short-term context<br/>(24h TTL)"]
    RC -- "No or expired" --> HIST[("Neon<br/>last N messages")]
    HIST --> CTX
    CTX --> RAG[("Neon pgvector<br/>agent_memories WHERE user_id = uid")]
    RAG --> PR["Build prompt<br/>context + memories + tools"]
    PR --> LLM["LLM<br/>Gemini / Groq"]
    LLM --> FC{"Function call?"}
    FC -- "search_restaurants" --> Q[("Neon PostGIS<br/>restaurant search")]
    Q --> LLM
    FC -- "get_favorites" --> FS[("Firestore<br/>users/uid/favorites")]
    FS --> LLM
    FC -- "No: final answer" --> RESP["Streamed response<br/>+ restaurant cards"]
    RESP --> APP["Flutter App"]
    RESP --> W1["Redis<br/>save context, reset TTL"]
    RESP --> W2[("Neon<br/>persist messages")]
    RESP --> W3["Extract preferences<br/>embed"]
    W3 --> W4[("Neon<br/>upsert agent_memories")]
```

#### Authentication Flow

```mermaid
sequenceDiagram
    autonumber
    actor U as User
    participant App as Flutter App
    participant FA as Firebase Auth
    participant FS as Firestore
    participant GW as API Gateway
    participant L as Lambda (agent)
    participant R as Redis
    participant DB as Neon PostgreSQL

    rect rgba(0, 0, 0, 0)
    Note over U,FS: Sign in (Firebase owns identity and user data)
    U->>App: Email and password
    App->>FA: Sign in
    FA-->>App: ID token (JWT) + refresh token
    App->>FS: Read users/{uid} (Security Rules: auth.uid == uid)
    FS-->>App: Profile and settings (theme, notifications, reports)
    App->>FS: Register FCM token in users/{uid}/devices
    App-->>U: Home (session persisted by the SDK)
    end

    rect rgba(0, 0, 0, 0)
    Note over U,DB: Open the agent chat (Neon owns the agent data)
    U->>App: Open Chat tab
    App->>FA: Get ID token (auto refresh if expired)
    FA-->>App: Valid JWT
    App->>GW: GET /agent/conversations (Bearer JWT)
    GW->>FA: Validate signature with JWKS, issuer, audience and expiry
    alt Valid token
        FA-->>GW: Keys
        GW->>L: Invoke with uid (sub claim)
        L->>DB: SELECT conversations WHERE user_id = uid
        DB-->>L: User's conversations
        L-->>App: 200 OK
    else Invalid or expired token
        GW-->>App: 401 Unauthorized
        App->>FA: Force token refresh or sign in again
    end

    U->>App: Open a conversation
    App->>GW: GET /agent/conversations/{id}/messages (Bearer JWT)
    GW->>L: Invoke with uid
    L->>DB: Check conversation belongs to uid (404 if not)
    L->>R: Get chat:{uid}:{id}
    alt Context in Redis
        R-->>L: Recent context (24h TTL)
    else Expired or new
        L->>DB: Load permanent messages
        DB-->>L: Messages
        L->>R: Rebuild context and set TTL
    end
    L-->>App: Messages
    App-->>U: Conversation ready
    Note over App,DB: Sending a message continues in the Agent Chat Flow (memories filtered by user_id)
    end

    U->>App: Settings > Exit
    App->>FA: Sign out
    App->>App: Clear Hive and local session
```

#### Nearby Search Flow

```mermaid
sequenceDiagram
    autonumber
    actor U as User
    participant App as Flutter App
    participant H as Hive
    participant GW as API Gateway
    participant L as Lambda (restaurants)
    participant R as Redis
    participant DB as Neon PostgreSQL

    U->>App: Move map / search / apply filters
    App->>H: Read cached results
    H-->>App: Cached restaurants (if any)
    App->>GW: GET /restaurants/nearby (Firebase JWT)
    GW->>GW: Validate JWT (JWKS)
    GW->>L: Invoke
    L->>R: Get cache by geohash + filters hash
    alt Cache hit
        R-->>L: Cached results
    else Cache miss
        L->>DB: PostGIS query (radius, labels, properties, price)
        DB-->>L: Restaurants
        L->>R: Set cache (TTL)
    end
    L-->>GW: 200 OK
    GW-->>App: Restaurants JSON
    App->>H: Update local cache
    App-->>U: Render markers on map
    U->>App: Tap a pin
    App->>GW: GET /restaurants/{id}
    GW-->>App: Details (rating, description, images)
```

### 2.2- System Performance & Scalability Design

#### Client & Caching Strategy

- **Local Persistence:** Hive for ultra-fast local storage (recommendations, active filters, theme, recent chat messages).
- **Offline Data:** Firestore offline persistence for favorites, profile and settings.
- **Asset Caching:** `cached_network_image` for LRU disk/memory caching of restaurant imagery.
- **UI State & UX:** `flutter_bloc` for isolated widget re-renders with lazy loading & infinite scrolling on home sections, map and list views.

#### Infrastructure & API Layer

- **IaC & Deployment:** Modular Terraform for AWS persistent infrastructure combined with AWS SAM for serverless function testing and Canary deployments. Firebase rules and indexes are deployed with the Firebase CLI.
- **Asynchronous Task Queue:** AWS SQS for decoupling push notifications, periodic reports, and heavy background processing. EventBridge Scheduler triggers the reports.
- **Authentication at the Edge:** API Gateway JWT authorizer validates the Firebase token before invoking any Lambda; the Firebase UID (`sub` claim) identifies the user in every request.
- **Traffic Control:** Rate limiting and throttling enforced at the API Gateway layer to protect backend Lambdas.

#### User Data Layer (Firebase)

- **Authentication:** Firebase Authentication handles sign up, sign in, email and password changes, and token issuing.
- **Per-User Data:** Cloud Firestore stores profile, settings and favorites per user.
- **Isolation:** Firestore Security Rules allow each user to read and write only their own documents.

#### Database & Storage Layer (Neon PostgreSQL)

- **Scope:** Restaurant details and everything related to the agent does not store credentials or profile data.
- **Connection Management:** Connection pooling via PgBouncer to sustain high-concurrency Lambda bursts without port exhaustion.
- **Compute Efficiency:** Autosuspend and Autoresume enabled for dynamic traffic-based resource scaling.
- **Data Ownership:** Every agent table is keyed per user there are no cross-database foreign keys.

#### Serverless Compute (AWS Lambda)

- **Architecture:** arm64 execution environment for optimized cost-performance ratio.
- **Connection Reuse:** Database and Redis clients instantiated outside handlers for warm invocation reuse.
- **Networking:** HTTP Keep-Alive enabled for low-latency TLS handshakes with LLM services.
- **Low-Latency Streaming:** Response Streaming enabled for fast TTFT user experience in chat interactions.

#### AI Agent Memory Architecture

- **Ephemeral Context:** Redis with 24-hour TTL for instant short-term conversational context and state (immediate memory of the agent).
- **Persistent Chat Storage:** Neon PostgreSQL for permanent conversations and messages, one set per user.
- **Semantic Retrieval:** Vectorized embeddings in Neon as persistent memory of the agent, for RAG-based preference matching.

```mermaid
flowchart LR
    Msg["User message<br/>(uid + conversation_id)"] --> Ctx{"Context in Redis?<br/>chat:uid:conversation"}
    Ctx -- "Yes" --> Load["Short-term context"]
    Ctx -- "No or expired" --> Hist["Last N messages<br/>from Neon"]
    Hist --> Load
    Load --> Emb["Generate message embedding"]
    Emb --> RAG["pgvector similarity search<br/>agent_memories WHERE user_id = uid"]
    RAG --> Prompt["Build prompt<br/>context + memories + tools"]
    Prompt --> LLM["LLM"]
    LLM --> Resp["Streamed response"]
    Resp --> Save["Update Redis<br/>(reset 24h TTL)"]
    Resp --> Persist["Persist messages<br/>in Neon (permanent)"]
    Resp --> Extract["Extract new preferences"]
    Extract --> Vec["Embed and upsert<br/>agent_memories in Neon"]
```

### 2.3- Tech Stack

#### Tools

- **Frontend:** Flutter Framework, Dart Language
- **Backend Runtime:** Node.js, AWS Lambda (Serverless)
- **Authentication & User Data:** Firebase Authentication, Cloud Firestore
- **Database (Catalog & Agent):** Neon PostgreSQL Serverless
- **Geospatial & Vector Search:** PostGIS, `pgvector`
- **In-Memory Cache:** Amazon ElastiCache (Redis)
- **API Management:** AWS API Gateway (HTTP API with JWT authorizer)
- **Push Notifications:** Firebase Cloud Messaging
- **Scheduling & Queues:** Amazon EventBridge Scheduler, AWS SQS
- **Local Storage:** Hive

#### Packages

**Client**
- SDK: Flutter SDK with `dart`
- State Management: `flutter_bloc`
- Navigation: `go_router`
- Dependency Injection: `get_it`
- Local Cache: `hive`, `hive_flutter`, `build_runner`
- Data Models & Serialization: `freezed`, `json_serializable`
- Geospatial & Maps: `google_maps_flutter`, `geolocator`
- Network & Media: `dio`, `cached_network_image`
- Firebase: `firebase_core`, `firebase_auth`, `cloud_firestore`, `firebase_messaging`

**Infrastructure & Backend**
- Infrastructure as Code (IaC): `terraform`
- Cache & Sessions: Redis (`ioredis`)
- Database ORM / Driver: `drizzle-orm`, `drizzle-kit` (migrations), `pg` (Neon pooled connection)
- Schema Validation: `zod`
- AI Agent & Orchestration: Google Gemini API (`@google/genai`, LLM and embeddings), Groq API (`groq-sdk`, fallback), Ollama (local development only)
- Firebase: `firebase-admin` (Firestore access for agent tools, FCM sending, account deletion)
- Bundling & SDKs: `esbuild`, `@aws-sdk/client-*`

**DevOps**
- Deployment & Serverless Testing: AWS SAM
- Local Infrastructure: Docker & Docker Compose, Firebase Emulator Suite
- Version Control: Git with `conventional commits`
- Static Analysis & Linters: `flutter_lints`, `dart format`, `prettier`, `hadolint`, `tflint`
- CI/CD: GitHub Actions
- Observability: AWS CloudWatch, AWS X-Ray

#### Standardization

- Google design rules with `flutter_lints`
- Automatic formatting with `dart format` and `prettier`
- Commit messages with `conventional commits`

### 2.4- Folder Structure (Feature-First)

#### Frontend/

```
lib/
  app/                  # global settings, routes and themes
    config/             # endpoints and variables 
    router/             # screen routes
    theme/              # color variables, palette and themes
  core/                 # shared logic between features
    location/           # distance calculations, geolocation
    network/            # http client and api endpoints
    storage/            # local storage (hive)
    utils/              # extensions, helpers
    widgets/            # shared components
  features/             # business logic separated by features
    auth/               # login, register, firebase session
    home/               # recommendations, promotions and sections
    explore/            # map, search, labels, location details
    filters/            # radius, time of arrival, properties, price
    agent/              # llm chat, conversations, restaurant cards
    restaurants/        # shared logic with data
    favorites/          # saved restaurants (firestore)
    settings/           # account, theme, notifications, reports
```

#### Backend/

```
src/
  api/                  # lambda entrypoints
    restaurants/        # nearby search, details, labels and properties
    discover/           # home sections: of the day, promotions, by preference
    agent/              # conversations, messages and streaming
    account/            # account deletion and data erasure
  core/                 # config, constants, errors and auth claims (uid)
  infrastructure/       # services and databases
    neon/               # pg client, drizzle schema and migrations
    redis/              # ioredis client and key helpers
    firebase/           # firebase-admin (firestore, auth, fcm)
    llm/                # gemini, groq and embeddings clients
    queue/              # sqs producers
  models/               # interfaces and types restaurant, conversation, memory
  orchestrator/         # agent logic and function calling
    memory/             # redis context, neon history, pgvector retrieval
    tools/              # search_restaurants, get_favorites
  workers/              # sqs consumers: notifications and reports
```

### 2.5- Data Model

Two separate databases. They are linked only by the Firebase UID (`user_id`), with no cross-database foreign keys. Restaurants are shared data; favorites keep a snapshot of the restaurant for fast rendering.

#### Cloud Firestore (User Data)

```mermaid
%%{init: {"er": {"useMaxWidth": false, "fontSize": 11, "minEntityWidth": 90, "entityPadding": 8}, "themeVariables": {"fontSize": "11px"}}}%%
erDiagram
    direction LR
    USERS ||--o{ FAVORITES : "subcollection"
    USERS ||--o{ DEVICES : "subcollection"

    USERS {
        string uid PK "Firebase UID"
        string name
        string email
        string photo_url
        boolean dark_theme
        boolean notifications
        boolean reports
        map filters "last used filters"
        timestamp created_at
    }
    FAVORITES {
        string restaurant_id PK
        string name
        string image_url
        string description
        float rating
        timestamp saved_at
    }
    DEVICES {
        string fcm_token PK
        string platform
        timestamp updated_at
    }
```

#### Neon PostgreSQL (Catalog & Agent)

```mermaid
erDiagram
    RESTAURANTS ||--o{ RESTAURANT_LABELS : has
    LABELS ||--o{ RESTAURANT_LABELS : tags
    RESTAURANTS ||--o{ RESTAURANT_PROPERTIES : has
    PROPERTIES ||--o{ RESTAURANT_PROPERTIES : describes
    RESTAURANTS ||--o{ PROMOTIONS : offers
    CONVERSATIONS ||--o{ MESSAGES : contains

    RESTAURANTS {
        uuid id PK
        string name
        text description
        string address
        geography location "PostGIS point"
        float rating
        numeric avg_price
        jsonb image_urls
        jsonb opening_hours
        vector embedding "pgvector"
    }
    LABELS {
        uuid id PK
        string name UK
    }
    PROPERTIES {
        uuid id PK
        string name UK
    }
    RESTAURANT_LABELS {
        uuid restaurant_id FK
        uuid label_id FK
    }
    RESTAURANT_PROPERTIES {
        uuid restaurant_id FK
        uuid property_id FK
    }
    PROMOTIONS {
        uuid id PK
        uuid restaurant_id FK
        string title
        text description
        timestamp starts_at
        timestamp ends_at
    }
    CONVERSATIONS {
        uuid id PK
        string user_id "Firebase UID"
        string title
        timestamp created_at
        timestamp updated_at
    }
    MESSAGES {
        uuid id PK
        uuid conversation_id FK
        string role "user or assistant"
        text content
        jsonb metadata "restaurant ids for cards"
        timestamp created_at
    }
    AGENT_MEMORIES {
        uuid id PK
        string user_id "Firebase UID"
        text content "preference or fact"
        vector embedding "pgvector"
        timestamp created_at
        timestamp updated_at
    }
```

#### Redis (Ephemeral)

| Key pattern                           | Content                              | TTL            |
| :------------------------------------ | :----------------------------------- | :------------- |
| `chat:{uid}:{conversationId}`         | Recent messages and agent state      | 24 hours       |
| `nearby:{geohash}:{filtersHash}`      | Cached restaurants for a map area    | Short (minutes)|
| `discover:{uid}:{date}`               | Cached Home sections for the day     | Until midnight |

### 2.6- API Reference

#### Backend REST API (Lambda)

All endpoints require a Firebase JWT in the `Authorization: Bearer <token>` header, validated by the API Gateway JWT authorizer. The user is identified by the `sub` claim (Firebase UID).

| Module        | Method   | Endpoint                             | Description                                      |
| :------------ | :------: | :----------------------------------- | :----------------------------------------------- |
| Discover      | `GET`    | `/discover`                          | Recommendations of the day, promotions           |
| Discover, Map | `GET`    | `/restaurants/nearby`                | Nearby restaurants, query parameters             |
| Discover, Map | `GET`    | `/restaurants/{id}`                  | Location details, rating, description and images |
| Map           | `POST`   | `/restaurants/map/bounds`            | Get visible marks to the map                     |
| Map           | `GET`    | `/restaurants/autocomplete`          | Complete options from search input               |
| Filters       | `GET`    | `/restaurants/filters/options`       | Consolidates properties and labels               |
| Agent         | `GET`    | `/agent/conversations`               | List the user's conversations                    |
| Agent         | `POST`   | `/agent/conversations`               | Create a conversation                            |
| Agent         | `GET`    | `/agent/conversations/{id}/messages` | Permanent history of a conversation              |
| Agent         | `POST`   | `/agent/conversations/{id}/messages` | Send message response with restaurant cards      |
| Agent         | `DELETE` | `/agent/conversations/{id}`          | Delete a conversation and its Redis context      |
| Favorites     | `GET`    | `/restaurants`                       | Batch by ids to refresh favorites                |
| Account       | `DELETE` | `/account`                           | Erase user data in Neon, Redis, Firebase         |

#### Firebase SDK (Client)

These operations go directly from the app to Firebase and do not pass through the backend.

| Feature        | Service          | Operation                                                                      |
| :------------- | :--------------- | :----------------------------------------------------------------------------- |
| Sign up / in   | Authentication   | Create account and sign in with email and password; sign out (Exit)            |
| Name           | Firestore        | Update `users/{uid}.name` (and Auth display name)                              |
| Email          | Authentication   | Change email (requires recent login)                                           |
| Password       | Authentication   | Change password (requires recent login)                                        |
| Dark Theme     | Firestore        | Update `users/{uid}.dark_theme`                                                |
| Notifications  | Firestore        | Update `users/{uid}.notifications` and register the FCM token in `devices`     |
| Reports        | Firestore        | Update `users/{uid}.reports`                                                   |
| Favorites      | Firestore        | Create, read and delete `users/{uid}/favorites/{restaurantId}`                 |
| Filters        | Firestore        | Save last used filters in `users/{uid}.filters`                                |

<br>

## 3- Development and Execution Guide

### 3.1- Setup Environment

#### Pre-Requisites

- Flutter SDK `>=3.19.0`
- Dart SDK `>=3.3.0`
- Node.js `>=18.x.x` & npm/pnpm
- Docker & Docker Compose
- AWS CLI configured
- Android Studio / Xcode
- Firebase project (Authentication, Cloud Firestore and Cloud Messaging enabled)
- Firebase CLI & FlutterFire CLI
- Neon project with the `postgis` and `vector` extensions enabled
- Google Maps API key
- Gemini API key

### 3.2 Local Execution

#### Frontend

- Go frontend folder `cd frontend`
- Clean cache `flutter clean`
- Install dependencies `flutter pub get`
- Configure Firebase `flutterfire configure`
- Generate code (freezed, json_serializable, hive) `dart run build_runner build --delete-conflicting-outputs`
- Static code analysis `flutter analyze`
- Run Unit Tests `flutter test`
- Run project `flutter run`

#### Backend

- Go to backend folder `cd backend`
- Install dependencies `npm install`
- Setup environment variables `cp .env.example .env`
- Start local infrastructure (PostgreSQL with PostGIS and pgvector, Redis) `docker compose up -d`
- Start Firebase emulators (Auth and Firestore) `firebase emulators:start --only auth,firestore`
- Run local database `npm run migrate`
- Seed restaurant catalog `npm run seed`
- Run Unit Tests `npm test`
- Start local serverless backend `npm run dev`

### 3.3- Environment Variables

| Variable                      | Used in  | Description                                                  |
| :---------------------------- | :------: | :----------------------------------------------------------- |
| `API_BASE_URL`                | Frontend | API Gateway base URL per environment                         |
| `GOOGLE_MAPS_API_KEY`         | Frontend | Google Maps SDK key (Android / iOS)                          |
| `GEMINI_API_KEY`              | Backend  | Google Gemini API key (LLM and embeddings)                   |
| `NEON_DATABASE_URL`           | Backend  | Neon PostgreSQL pooled connection string (catalog and agent) |
| `FIREBASE_PROJECT_ID`         | Backend  | Firebase project, used as JWT issuer/audience at the gateway |
| `FIREBASE_CLIENT_EMAIL`       | Backend  | Firebase Admin service account email                         |
| `FIREBASE_PRIVATE_KEY`        | Backend  | Firebase Admin private key                                   |
| `REDIS_URL`                   | Backend  | ElastiCache / local Redis URL                                |
| `SQS_QUEUE_URL`               | Backend  | Queue for notifications and reports                          |

### 3.4- CI/CD and Deployment

```mermaid
flowchart LR
    Dev["Push / PR"] --> CI
    
    subgraph CI["GitHub Actions: CI"]
        direction LR
        Lint["Lint & Format"] --> Test["Unit Tests"] --> Build["Build"]
    end
    
    CI --> Merge{"Merge to main?"}
    Merge -- "No" --> End["Review"]
    Merge -- "Yes" --> TF["Terraform Plan/Apply"]
    
    TF --> FB["Firebase CLI<br/>Firestore Rules + Indexes"]
    FB --> Mig["Drizzle Migrations<br/>(Neon)"]
    Mig --> SAM["AWS SAM Deploy<br/>(Canary)"]
    SAM --> Mon{"Healthy?"}
    
    Mon -- "Yes" --> Done["100% Traffic"]
    Mon -- "No" --> RB["Auto Rollback"]
```
