# Smart Hostel Surveillance & Security Management System

An automated, real-time smart surveillance and hostel security management platform engineered for institutional safety, multi-hostel administration, low-latency live video streaming, and rapid incident response.

---

## 🌟 Key Highlights & Features

- **Ultra-Low Latency Video Streaming (WebRTC & Mediasoup SFU)**: Real-time CCTV/RTSP camera feed ingestion and sub-second WebRTC broadcasting to security staff and admins.
- **Role-Based Access Control (RBAC)**: Multi-tiered privilege levels:
  - **Super User**: Global hostel setup, admin privilege management, and cross-hostel audits.
  - **Hostel Admin / Warden**: Hostel-level oversight, camera configuration, and student rosters.
  - **Security Guard**: Live multi-camera monitoring, visitor entry authorization, and incident escalation.
  - **Student**: Personal profile, access logging, and emergency panic button.
- **Real-Time WebSocket Alert System**: Instant bidirectional notifications and panic alerts delivered directly to assigned hostel guards.
- **Automated Video Recording & Playback**: Automatic background FFmpeg capture and chunked storage with historical playback endpoints.
- **Visitor Access & Pass Management**: Secure visitor pass check-in/check-out with QR verification.
- **Batch Data Operations**: High-speed CSV upload pipelines for bulk onboarding of students and personnel.

---

## 🏗️ Architecture Overview

```
+-----------------------------------------------------------------------------------+
|                                  CLIENT LAYER                                     |
|  +---------------------------------------+  +----------------------------------+  |
|  |     Mobile App (React Native / Expo)  |  |    Web Dashboard (React / Vite)  |  |
|  |   - Guard / Admin / Student Portals   |  |   - Multi-Hostel Security Panel  |  |
|  +---------------------------------------+  +----------------------------------+  |
+-----------------------------------------------------------------------------------+
                                   |                 ^
                     REST API / WS |                 | WebRTC Video Stream
                                   v                 |
+-----------------------------------------------------------------------------------+
|                                 SERVICES LAYER                                    |
|                                                                                   |
|  +------------------------------+       +--------------------------------------+  |
|  |     Node.js Backend (3000)   |       |       SFU Server (Mediasoup 2000)    |  |
|  | - Auth (JWT & Role RBAC)     |       | - Low-latency WebRTC Video Forwarding|  |
|  | - MongoDB Ingestion & CRUD   |       | - RTP Packet Ingestion from MediaMTX |  |
|  | - WebSocket Live Alerts      |       +--------------------------------------+  |
|  | - FFmpeg Recording Manager   |                          ^                      |
|  +------------------------------+                          | RTP Packet Stream    |
|                 |                                          |                      |
|                 v                       +--------------------------------------+  |
|      +---------------------+            |      MediaMTX Server (RTSP 8554)     |  |
|      |  MongoDB Database   |            | - CCTV / IP Camera RTSP Stream Ingest|  |
|      +---------------------+            +--------------------------------------+  |
+-----------------------------------------------------------------------------------+
```

---

## 🛠️ Technology Stack

| Layer | Technologies |
| :--- | :--- |
| **Mobile Application** | React Native, Expo, Expo Router, TypeScript, Expo Secure Store |
| **Web Dashboard** | React, TypeScript, React Router, HTML5 Video, WebSocket Client |
| **Backend API** | Node.js, Express, TypeScript, Mongoose, Zod, JWT, bcrypt, Multer, CSV-Parse |
| **Real-Time Video (SFU)** | Mediasoup, WebRTC, SDP/RTP, WebSocket Signaling, FFmpeg |
| **Media Server** | MediaMTX (RTSP/WebRTC/HLS Ingestion & Routing) |
| **Database** | MongoDB (NoSQL) |
| **Shared Core** | Monorepo Shared Types & Zod Validation Schemas |

---

## 📁 Repository Structure

```
.
├── mobile/               # React Native (Expo) mobile application for admins, guards, students
├── node_backend/         # Express REST API, MongoDB models, WebSocket notification server
├── sfu_server/           # Mediasoup WebRTC Selective Forwarding Unit for video routing
├── mediaMTX_server/      # MediaMTX configuration and entrypoints for RTSP stream ingest
├── frontend/             # React web management dashboard
├── shared/               # Shared TypeScript types, Zod schemas, and data contracts
├── scripts/              # Docker build, start, and service management shell scripts
├── start.sh              # Multi-process development launcher script (tmux)
└── package.json          # Root workspace configuration
```

---

## 🚀 Getting Started

### Prerequisites
- **Node.js**: v18+ & **npm**
- **MongoDB**: Community Server (running locally or MongoDB Atlas)
- **FFmpeg**: Installed on system path
- **MediaMTX**: Download binary from [MediaMTX Releases](https://github.com/bluenviron/mediamtx/releases) into `mediaMTX_server/`

### 1. Environment Setup
Create `.env` inside `node_backend/`:
```env
PORT=3000
MONGO_URL=mongodb://127.0.0.1:27017/hostel_security
JWT_SECRET=your_jwt_secret_key
```

Create `.env` inside `sfu_server/`:
```env
PORT=2000
MONGO_URL=mongodb://127.0.0.1:27017/hostel_security
JWT_SECRET=your_jwt_secret_key
MEDIAMTX_IP=127.0.0.1
```

### 2. Install Dependencies
From the repository root:
```bash
npm install
```

### 3. Run Services
* **Start Node Backend**:
  ```bash
  cd node_backend
  npm run dev
  ```

* **Start SFU Media Server**:
  ```bash
  cd sfu_server
  npm run dev
  ```

* **Start Mobile App**:
  ```bash
  cd mobile
  npx expo start
  ```

---

## 🔒 Security & RBAC Design
- Passwords hashed using industry-standard `bcrypt` salts.
- Authenticated endpoints protected by stateless JWT authorization headers.
- Multi-hostel tenant isolation: Admins and guards are scoped strictly to their `allocatedHostel`, preventing unauthorized cross-hostel access.

---

## 📄 License
This project is open-source and available under the [MIT License](LICENSE).
