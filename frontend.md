# QuMail - Quantum Secure Email Client

## Complete Developer Documentation

---

# Table of Contents

1. [Project Overview](#1-project-overview)
2. [Architecture](#2-architecture)
3. [Technology Stack](#3-technology-stack)
4. [Directory Structure](#4-directory-structure)
5. [Getting Started](#5-getting-started)
6. [Electron Main Process](#6-electron-main-process)
7. [React Application](#7-react-application)
8. [State Management](#8-state-management)
9. [Components Reference](#9-components-reference)
10. [Styling Guide](#10-styling-guide)
11. [Security Features](#11-security-features)
12. [IPC Communication](#12-ipc-communication)
13. [API Reference](#13-api-reference)
14. [Testing](#14-testing)
15. [Build & Deployment](#15-build--deployment)
16. [Troubleshooting](#16-troubleshooting)
17. [Contributing](#17-contributing)

---

# 1. Project Overview

## 1.1 What is QuMail?

QuMail is a cross-platform desktop email client built with Electron and React that integrates **Quantum Key Distribution (QKD)** for enhanced email security. It provides enterprise-grade encryption using quantum-generated keys while maintaining a user-friendly interface similar to Microsoft Outlook.

## 1.2 Key Features

| Feature | Description |
|---------|-------------|
| **Quantum Encryption** | Four security levels including One-Time Pad (OTP) |
| **Key Management** | Integration with Quantum Key Manager (KM) systems |
| **Cross-Platform** | Runs on Windows, macOS, and Linux |
| **Modern UI** | Clean, responsive interface with dark/light themes |
| **Security Dashboard** | Real-time analytics and security monitoring |
| **Offline Capable** | Local key caching for offline encryption |

## 1.3 Security Levels

```
Level 1: Quantum Secure (OTP)      → Theoretically unbreakable
Level 2: Quantum-aided AES-256     → Strong quantum-enhanced encryption  
Level 3: Post-Quantum Crypto (PQC) → Future-proof hybrid encryption
Level 4: Standard Encryption       → Traditional TLS/SSL only
```

## 1.4 System Requirements

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| OS | Windows 10, macOS 10.14, Ubuntu 18.04 | Latest versions |
| RAM | 4 GB | 8 GB |
| Storage | 200 MB | 500 MB |
| Node.js | 18.x | 20.x LTS |
| Display | 1024x768 | 1920x1080 |

---

# 2. Architecture

## 2.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           QuMail Application                             │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌──────────────────────────────────────────────────────────────────┐   │
│  │                     Electron Main Process                         │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │   │
│  │  │   Window    │  │    IPC      │  │    Native Modules       │  │   │
│  │  │  Manager    │  │  Handlers   │  │  (Crypto, File System)  │  │   │
│  │  └─────────────┘  └─────────────┘  └─────────────────────────┘  │   │
│  └──────────────────────────────────────────────────────────────────┘   │
│                                    │                                     │
│                              IPC Bridge                                  │
│                            (contextBridge)                               │
│                                    │                                     │
│  ┌──────────────────────────────────────────────────────────────────┐   │
│  │                    Electron Renderer Process                      │   │
│  │  ┌─────────────────────────────────────────────────────────────┐ │   │
│  │  │                      React Application                       │ │   │
│  │  │  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌──────────┐ │ │   │
│  │  │  │  Screens  │  │Components │  │   Hooks   │  │  Utils   │ │ │   │
│  │  │  └───────────┘  └───────────┘  └───────────┘  └──────────┘ │ │   │
│  │  │                         │                                    │ │   │
│  │  │  ┌──────────────────────────────────────────────────────┐  │ │   │
│  │  │  │              Zustand State Management                 │  │ │   │
│  │  │  └──────────────────────────────────────────────────────┘  │ │   │
│  │  └─────────────────────────────────────────────────────────────┘ │   │
│  └──────────────────────────────────────────────────────────────────┘   │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    │               │               │
                    ▼               ▼               ▼
            ┌───────────┐   ┌───────────┐   ┌───────────┐
            │   Email   │   │  Quantum  │   │  Local    │
            │  Servers  │   │    KM     │   │  Storage  │
            │(IMAP/SMTP)│   │   APIs    │   │           │
            └───────────┘   └───────────┘   └───────────┘
```

## 2.2 Data Flow

```
User Action → React Component → Zustand Store → IPC (if needed) → Main Process
                                      ↓
                              State Update → UI Re-render
```

## 2.3 Process Communication

```
┌─────────────────┐                      ┌─────────────────┐
│  Main Process   │                      │ Renderer Process│
│                 │                      │    (React)      │
│  - File System  │   ←── IPC ───→      │  - UI Logic     │
│  - Encryption   │   contextBridge      │  - State        │
│  - KM API Calls │                      │  - Components   │
│  - System Tray  │                      │                 │
└─────────────────┘                      └─────────────────┘
```

---

# 3. Technology Stack

## 3.1 Core Technologies

| Layer | Technology | Version | Purpose |
|-------|------------|---------|---------|
| **Desktop Framework** | Electron | ^28.0.0 | Cross-platform desktop app |
| **Frontend Framework** | React | ^18.2.0 | UI component library |
| **State Management** | Zustand | ^4.4.7 | Lightweight state management |
| **Styling** | Tailwind CSS | ^3.3.6 | Utility-first CSS framework |
| **Animation** | Framer Motion | ^10.16.16 | Smooth animations |
| **Charts** | Recharts | ^2.10.3 | Data visualization |
| **Icons** | Lucide React | ^0.294.0 | Icon library |
| **Date Handling** | date-fns | ^2.30.0 | Date utilities |

## 3.2 Development Dependencies

| Package | Purpose |
|---------|---------|
| `electron-builder` | Packaging and distribution |
| `concurrently` | Run multiple commands |
| `wait-on` | Wait for resources |
| `react-scripts` | React development tools |
| `autoprefixer` | CSS vendor prefixes |
| `postcss` | CSS processing |

## 3.3 Dependency Graph

```
qumail
├── electron (main process)
│   ├── electron-builder
│   └── native modules
├── react (renderer)
│   ├── zustand
│   │   └── immer (immutable updates)
│   ├── framer-motion
│   ├── recharts
│   ├── lucide-react
│   └── date-fns
└── tailwindcss
    ├── postcss
    └── autoprefixer
```

---

# 4. Directory Structure

```
qumail/
│
├── 📁 electron/                    # Electron main process
│   ├── main.js                     # Main entry point
│   ├── preload.js                  # Preload script (context bridge)
│   └── ipc-handlers.js             # IPC event handlers
│
├── 📁 public/                      # Static assets
│   ├── index.html                  # HTML template
│   ├── icon.png                    # App icon
│   └── tray-icon.png               # System tray icon
│
├── 📁 src/                         # React application source
│   │
│   ├── 📄 index.jsx                # React entry point
│   ├── 📄 App.jsx                  # Root component
│   ├── 📄 index.css                # Global styles + Tailwind
│   │
│   ├── 📁 screens/                 # Top-level screen components
│   │   ├── index.js                # Screen exports
│   │   ├── SplashScreen.jsx        # Loading/splash screen
│   │   ├── LoginScreen.jsx         # Authentication screen
│   │   └── MainDashboard.jsx       # Main application screen
│   │
│   ├── 📁 components/              # Reusable UI components
│   │   │
│   │   ├── 📁 TitleBar/
│   │   │   └── TitleBar.jsx        # Custom window title bar
│   │   │
│   │   ├── 📁 Sidebar/
│   │   │   └── Sidebar.jsx         # Navigation sidebar
│   │   │
│   │   ├── 📁 Toolbar/
│   │   │   └── Toolbar.jsx         # Action toolbar
│   │   │
│   │   ├── 📁 EmailList/
│   │   │   └── EmailList.jsx       # Email list view
│   │   │
│   │   ├── 📁 EmailReader/
│   │   │   └── EmailReader.jsx     # Email reading pane
│   │   │
│   │   ├── 📁 Compose/
│   │   │   └── ComposeModal.jsx    # Email composition
│   │   │
│   │   ├── 📁 KeyManager/
│   │   │   └── KeyManagerPanel.jsx # Quantum key management
│   │   │
│   │   ├── 📁 SecurityDashboard/
│   │   │   └── SecurityDashboard.jsx # Security analytics
│   │   │
│   │   ├── 📁 Settings/
│   │   │   └── SettingsPanel.jsx   # Application settings
│   │   │
│   │   └── 📁 Common/              # Shared components
│   │       ├── Modal.jsx           # Modal wrapper
│   │       ├── Tooltip.jsx         # Tooltip component
│   │       ├── ContextMenu.jsx     # Right-click menu
│   │       └── NotificationCenter.jsx # Toast notifications
│   │
│   ├── 📁 store/                   # State management
│   │   └── useStore.js             # Zustand store definition
│   │
│   ├── 📁 hooks/                   # Custom React hooks
│   │   └── useTheme.js             # Theme management hook
│   │
│   └── 📁 utils/                   # Utility functions
│       ├── helpers.js              # General helpers
│       └── encryption.js           # Encryption utilities
│
├── 📄 package.json                 # Dependencies & scripts
├── 📄 tailwind.config.js           # Tailwind configuration
├── 📄 postcss.config.js            # PostCSS configuration
└── 📄 README.md                    # Project readme
```

## 4.1 File Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| React Components | PascalCase | `EmailReader.jsx` |
| Hooks | camelCase with `use` prefix | `useStore.js` |
| Utilities | camelCase | `helpers.js` |
| Styles | Component name | `index.css` |
| Constants | SCREAMING_SNAKE_CASE | `API_ENDPOINTS` |

---

# 5. Getting Started

## 5.1 Prerequisites

```bash
# Check Node.js version (18+ required)
node --version

# Check npm version
npm --version

# Install Node.js if needed (using nvm)
nvm install 20
nvm use 20
```

## 5.2 Installation

```bash
# Clone the repository
git clone https://github.com/your-org/qumail.git
cd qumail

# Install dependencies
npm install

# If you encounter peer dependency issues
npm install --legacy-peer-deps
```

## 5.3 Running the Application

### Development Mode

```bash
# Run React + Electron together
npm start

# Or run separately:
# Terminal 1: React development server
npm run react-start

# Terminal 2: Electron (after React is running)
npm run electron-start
```

### React Only (Browser)

```bash
npm run react-start
# Opens http://localhost:3000
```

## 5.4 Environment Variables

Create a `.env` file in the root directory:

```env
# Application
REACT_APP_NAME=QuMail
REACT_APP_VERSION=1.0.0

# API Endpoints (for production)
REACT_APP_KM_ENDPOINT=https://km.example.com/api/v1
REACT_APP_EMAIL_API=https://mail.example.com/api

# Feature Flags
REACT_APP_ENABLE_PQC=true
REACT_APP_ENABLE_ANALYTICS=true

# Development
REACT_APP_MOCK_DATA=true
```

## 5.5 Configuration Files

### tailwind.config.js

```javascript
module.exports = {
  content: ["./src/**/*.{js,jsx}"],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        primary: { /* blue shades */ },
        secondary: { /* purple shades */ },
        quantum: {
          secure: '#10b981',  // green
          aided: '#3b82f6',   // blue
          hybrid: '#8b5cf6',  // purple
          none: '#6b7280',    // gray
        },
      },
    },
  },
  plugins: [],
}
```

### postcss.config.js

```javascript
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

---

# 6. Electron Main Process

## 6.1 Main Process Overview

The main process (`electron/main.js`) handles:
- Window creation and management
- System tray integration
- Application menu
- IPC communication with renderer
- Native OS integrations

## 6.2 Main.js Structure

```javascript
// electron/main.js

const { app, BrowserWindow, ipcMain, Menu, Tray } = require('electron');
const path = require('path');
const { setupIpcHandlers } = require('./ipc-handlers');

let mainWindow;
let tray;

// Window Creation
function createWindow() {
  mainWindow = new BrowserWindow({
    width: 1400,
    height: 900,
    minWidth: 1024,
    minHeight: 768,
    frame: false,              // Custom title bar
    titleBarStyle: 'hidden',
    webPreferences: {
      nodeIntegration: false,  // Security: disabled
      contextIsolation: true,  // Security: enabled
      preload: path.join(__dirname, 'preload.js'),
    },
  });

  // Load app
  const startUrl = isDev 
    ? 'http://localhost:3000' 
    : `file://${path.join(__dirname, '../build/index.html')}`;
  
  mainWindow.loadURL(startUrl);
  
  // Setup handlers
  setupIpcHandlers(ipcMain, mainWindow);
}

// App lifecycle
app.whenReady().then(createWindow);
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit();
});
```

## 6.3 Preload Script

The preload script (`electron/preload.js`) creates a secure bridge between main and renderer processes:

```javascript
// electron/preload.js

const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
  // Window Controls
  minimizeWindow: () => ipcRenderer.send('window-minimize'),
  maximizeWindow: () => ipcRenderer.send('window-maximize'),
  closeWindow: () => ipcRenderer.send('window-close'),
  
  // Secure Storage
  secureStore: (key, value) => ipcRenderer.invoke('secure-store', key, value),
  secureRetrieve: (key) => ipcRenderer.invoke('secure-retrieve', key),
  
  // Key Manager
  connectToKM: (config) => ipcRenderer.invoke('km-connect', config),
  getKeys: (params) => ipcRenderer.invoke('km-get-keys', params),
  
  // Email Operations
  sendEmail: (data) => ipcRenderer.invoke('send-email', data),
  fetchEmails: (folder) => ipcRenderer.invoke('fetch-emails', folder),
  
  // Event Listeners
  onMenuAction: (callback) => {
    ipcRenderer.on('menu-action', (_, action) => callback(action));
    return () => ipcRenderer.removeAllListeners('menu-action');
  },
});
```

## 6.4 IPC Handlers

```javascript
// electron/ipc-handlers.js

function setupIpcHandlers(ipcMain, mainWindow) {
  // Window Controls
  ipcMain.on('window-minimize', () => mainWindow.minimize());
  ipcMain.on('window-maximize', () => {
    mainWindow.isMaximized() ? mainWindow.unmaximize() : mainWindow.maximize();
  });
  ipcMain.on('window-close', () => mainWindow.close());
  
  // Async Handlers (invoke/handle pattern)
  ipcMain.handle('km-connect', async (event, config) => {
    // Connect to Key Manager
    return { success: true, connection: { ...config } };
  });
  
  ipcMain.handle('send-email', async (event, emailData) => {
    // Encrypt and send email
    return { success: true, messageId: `MSG-${Date.now()}` };
  });
}
```

## 6.5 Security Considerations

```javascript
// Security best practices in Electron

// 1. Disable Node.js in renderer
webPreferences: {
  nodeIntegration: false,
  contextIsolation: true,
}

// 2. Use contextBridge for IPC
contextBridge.exposeInMainWorld('api', { /* safe methods */ });

// 3. Validate all IPC inputs
ipcMain.handle('action', async (event, data) => {
  if (!validateInput(data)) throw new Error('Invalid input');
  // Process...
});

// 4. Use safeStorage for credentials
const { safeStorage } = require('electron');
const encrypted = safeStorage.encryptString(password);
```

---

# 7. React Application

## 7.1 Application Entry Point

```jsx
// src/index.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

## 7.2 App Component (Router)

```jsx
// src/App.jsx
import React, { useState, useEffect } from 'react';
import { SplashScreen, LoginScreen, MainDashboard } from './screens';
import useStore from './store/useStore';

function App() {
  const [appState, setAppState] = useState('splash');
  const { isAuthenticated, theme, initializeApp } = useStore();

  // Apply theme
  useEffect(() => {
    document.documentElement.classList.toggle('dark', 
      theme === 'dark' || 
      (theme === 'system' && window.matchMedia('(prefers-color-scheme: dark)').matches)
    );
  }, [theme]);

  // Initialize and show splash
  useEffect(() => {
    initializeApp();
    setTimeout(() => setAppState(isAuthenticated ? 'main' : 'login'), 2500);
  }, []);

  // Render based on state
  switch (appState) {
    case 'splash': return <SplashScreen />;
    case 'login': return <LoginScreen onSuccess={() => setAppState('main')} />;
    case 'main': return <MainDashboard onLogout={() => setAppState('login')} />;
    default: return <SplashScreen />;
  }
}

export default App;
```

## 7.3 Screen Components

### Screen Flow

```
SplashScreen (2-3s) → LoginScreen → MainDashboard
                            ↑              │
                            └──── Logout ──┘
```

### Screen Responsibilities

| Screen | Purpose | Key Features |
|--------|---------|--------------|
| `SplashScreen` | Loading/branding | Animated logo, progress bar |
| `LoginScreen` | Authentication | Email config, KM setup |
| `MainDashboard` | Main interface | Email list, reader, compose |

---

# 8. State Management

## 8.1 Zustand Store Overview

QuMail uses **Zustand** with **Immer** for immutable state updates. The store is defined in `src/store/useStore.js`.

## 8.2 Store Structure

```javascript
const useStore = create(immer((set, get) => ({
  // ═══════════════════════════════════════════
  // AUTH STATE
  // ═══════════════════════════════════════════
  isAuthenticated: false,
  user: null,                    // { email, name }
  emailAccounts: [],             // Connected email accounts

  // ═══════════════════════════════════════════
  // KEY MANAGER STATE
  // ═══════════════════════════════════════════
  kmConnection: null,            // KM connection details
  kmStatus: 'disconnected',      // disconnected | connecting | connected | error
  
  keys: {
    available: [],               // Keys ready to use
    inUse: [],                   // Currently active keys
    used: [],                    // Consumed keys
    expired: [],                 // Expired keys
    stats: {
      availableCount: 0,
      inUseCount: 0,
      usedCount: 0,
      expiredCount: 0,
      lastSync: null,
    }
  },

  // ═══════════════════════════════════════════
  // EMAIL STATE
  // ═══════════════════════════════════════════
  emails: {
    inbox: [],
    sent: [],
    drafts: [],
    trash: [],
    starred: [],
  },
  selectedFolder: 'inbox',
  selectedEmail: null,
  searchQuery: '',
  securityFilter: 'all',

  // ═══════════════════════════════════════════
  // UI STATE
  // ═══════════════════════════════════════════
  theme: 'system',               // light | dark | system
  sidebarCollapsed: false,
  showCompose: false,
  showSettings: false,
  showKeyManager: false,
  showSecurityDashboard: false,
  activeSettingsTab: 'general',
  composeData: null,             // For reply/forward

  // ═══════════════════════════════════════════
  // LOADING STATES
  // ═══════════════════════════════════════════
  isLoading: false,
  isSyncing: false,
  isSending: false,

  // ═══════════════════════════════════════════
  // NOTIFICATIONS
  // ═══════════════════════════════════════════
  notifications: [],             // Toast notifications

  // ═══════════════════════════════════════════
  // SETTINGS
  // ═══════════════════════════════════════════
  settings: {
    general: { /* ... */ },
    security: { /* ... */ },
    appearance: { /* ... */ },
    quantum: { /* ... */ },
  },

  // ═══════════════════════════════════════════
  // ACTIONS
  // ═══════════════════════════════════════════
  // ... (see next section)
})));
```

## 8.3 Store Actions

### Authentication Actions

```javascript
// Login
login: async (credentials) => {
  set((state) => { state.isLoading = true; });
  
  const result = await window.electronAPI?.authenticate(credentials);
  
  if (result?.success) {
    set((state) => {
      state.isAuthenticated = true;
      state.user = result.user;
      state.isLoading = false;
    });
    return { success: true };
  }
  
  set((state) => { state.isLoading = false; });
  return { success: false, error: result?.error };
},

// Logout
logout: async () => {
  await window.electronAPI?.logout();
  set((state) => {
    state.isAuthenticated = false;
    state.user = null;
    state.kmConnection = null;
    state.kmStatus = 'disconnected';
  });
},
```

### Email Actions

```javascript
// Fetch emails for a folder
fetchEmails: async (folder) => {
  set((state) => { state.isLoading = true; });
  
  const result = await window.electronAPI?.fetchEmails(folder);
  
  set((state) => {
    state.emails[folder] = result?.emails || generateMockEmails(folder);
    state.isLoading = false;
  });
},

// Send email
sendEmail: async (emailData) => {
  set((state) => { state.isSending = true; });
  
  const result = await window.electronAPI?.sendEmail(emailData);
  
  if (result?.success) {
    get().addNotification({
      type: 'success',
      title: 'Email Sent',
      message: `Email sent to ${emailData.to}`,
    });
  }
  
  set((state) => { state.isSending = false; });
  return result;
},

// Delete email
deleteEmail: async (emailId) => {
  const folder = get().selectedFolder;
  set((state) => {
    state.emails[folder] = state.emails[folder].filter(e => e.id !== emailId);
    if (state.selectedEmail?.id === emailId) {
      state.selectedEmail = null;
    }
  });
},
```

### Key Manager Actions

```javascript
// Connect to KM
connectToKM: async (config) => {
  set((state) => { state.kmStatus = 'connecting'; });
  
  const result = await window.electronAPI?.connectToKM(config);
  
  set((state) => {
    state.kmConnection = result?.connection;
    state.kmStatus = result?.success ? 'connected' : 'error';
  });
  
  if (result?.success) get().fetchKeys();
  return result;
},

// Fetch keys
fetchKeys: async () => {
  const result = await window.electronAPI?.getKeys({ count: 50 });
  const status = await window.electronAPI?.getKeyStatus();
  
  set((state) => {
    state.keys.available = result?.keys?.filter(k => k.status === 'available') || [];
    state.keys.inUse = result?.keys?.filter(k => k.status === 'inUse') || [];
    state.keys.stats = { ...status, lastSync: new Date().toISOString() };
  });
},
```

### UI Actions

```javascript
// Folder selection
setSelectedFolder: (folder) => {
  set((state) => {
    state.selectedFolder = folder;
    state.selectedEmail = null;
  });
  get().fetchEmails(folder);
},

// Email selection
setSelectedEmail: (email) => {
  set((state) => { state.selectedEmail = email; });
  if (email && !email.read) get().markAsRead(email.id);
},

// Modal controls
setShowCompose: (show, data = null) => {
  set((state) => {
    state.showCompose = show;
    state.composeData = data;
  });
},

// Theme
setTheme: (theme) => {
  set((state) => {
    state.theme = theme;
    state.settings.appearance.theme = theme;
  });
},

// Notifications
addNotification: (notification) => {
  const id = Date.now();
  set((state) => {
    state.notifications.push({ ...notification, id });
  });
  setTimeout(() => get().removeNotification(id), 5000);
},
```

## 8.4 Using the Store

```jsx
// In any component
import useStore from '../store/useStore';

function MyComponent() {
  // Select specific state
  const emails = useStore((state) => state.emails.inbox);
  const isLoading = useStore((state) => state.isLoading);
  
  // Select actions
  const { fetchEmails, setSelectedEmail } = useStore();
  
  // Or select multiple with shallow comparison
  const { emails, selectedEmail } = useStore(
    (state) => ({ 
      emails: state.emails.inbox, 
      selectedEmail: state.selectedEmail 
    }),
    shallow
  );
  
  return (/* ... */);
}
```

## 8.5 State Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         Zustand Store                            │
│                                                                  │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │  Auth    │    │  Emails  │    │   Keys   │    │    UI    │  │
│  │  State   │    │  State   │    │  State   │    │  State   │  │
│  └────┬─────┘    └────┬─────┘    └────┬─────┘    └────┬─────┘  │
│       │               │               │               │         │
│       └───────────────┴───────────────┴───────────────┘         │
│                               │                                  │
│                           Actions                                │
│                               │                                  │
└───────────────────────────────┼──────────────────────────────────┘
                                │
          ┌─────────────────────┼─────────────────────┐
          │                     │                     │
          ▼                     ▼                     ▼
    ┌──────────┐         ┌──────────┐         ┌──────────┐
    │  Login   │         │  Email   │         │ Settings │
    │  Screen  │         │   List   │         │  Panel   │
    └──────────┘         └──────────┘         └──────────┘
```

---

# 9. Components Reference

## 9.1 Component Hierarchy

```
App
├── SplashScreen
├── LoginScreen
└── MainDashboard
    ├── TitleBar
    ├── Sidebar
    ├── Toolbar
    ├── EmailList
    │   └── EmailListItem
    ├── EmailReader
    │   ├── SecurityBanner
    │   └── AttachmentItem
    ├── ComposeModal
    │   └── SecurityLevelSelector
    ├── KeyManagerPanel
    ├── SecurityDashboard
    ├── SettingsPanel
    └── NotificationCenter
```

## 9.2 Component Documentation

### TitleBar

Custom window title bar with traffic light controls.

```jsx
// Props: none (uses store directly)

// Features:
// - Window minimize/maximize/close buttons
// - KM connection status indicator
// - Available keys count
// - Drag region for window movement

// Usage:
<TitleBar />
```

### Sidebar

Navigation sidebar with folder list and quantum features.

```jsx
// Props:
interface SidebarProps {
  onLogout: () => void;
}

// Features:
// - Folder navigation (Inbox, Sent, Drafts, Trash, Starred)
// - Unread count badges
// - Quantum Keys section
// - Key Manager shortcut
// - Security Dashboard shortcut
// - Settings and Logout

// Usage:
<Sidebar onLogout={handleLogout} />
```

### EmailList

Displays list of emails with filtering and search.

```jsx
// Props: none (uses store)

// Features:
// - Virtual scrolling for performance
// - Security level icons
// - Search and filter support
// - Skeleton loading state
// - Click to select email

// State dependencies:
// - emails[selectedFolder]
// - searchQuery
// - securityFilter
// - isLoading
```

### EmailReader

Displays selected email content with security details.

```jsx
// Props: none (uses store - selectedEmail)

// Features:
// - Email header (from, to, subject, date)
// - Security banner with encryption details
// - HTML email body rendering
// - Attachment list with download
// - Reply/Forward/Delete actions

// Subcomponents:
// - SecurityBanner: Shows encryption info
// - AttachmentItem: Individual attachment display
```

### ComposeModal

Email composition modal with security options.

```jsx
// Props: none (uses store)

// Features:
// - To/Cc/Bcc fields
// - Subject line
// - Rich text editor toolbar
// - Security level selector
// - Attachment management
// - Send/Save Draft/Discard

// State:
// - composeData: For reply/forward pre-fill
// - isSending: Loading state

// Subcomponents:
// - SecurityLevelSelector: Dropdown for encryption level
```

### SecurityLevelSelector

Dropdown for selecting email encryption level.

```jsx
// Props:
interface SecurityLevelSelectorProps {
  value: 1 | 2 | 3 | 4;
  onChange: (level: number) => void;
  recipientStatus?: 'quantum' | 'maybe' | 'no';
}

// Levels:
// 1: Quantum Secure (OTP) - Green lock
// 2: Quantum-aided AES - Blue lock  
// 3: PQC Hybrid - Purple shield
// 4: No Quantum - Gray open lock
```

### KeyManagerPanel

Quantum key management interface.

```jsx
// Props: none (uses store)

// Features:
// - Key statistics cards
// - Usage chart (Recharts LineChart)
// - Key list table with tabs
// - Request new keys
// - Clear expired keys
// - Export report

// Tabs:
// - Available | In Use | Used | Expired
```

### SecurityDashboard

Security analytics and monitoring.

```jsx
// Props: none (uses store)

// Features:
// - Email count by security level (PieChart)
// - Weekly activity (BarChart)
// - Security events log
// - Key statistics
// - Export options
```

### SettingsPanel

Application settings with tabbed interface.

```jsx
// Props: none (uses store)

// Tabs:
// 1. General: Startup, notifications, language
// 2. Email: Account management
// 3. Security: Encryption policies, key settings
// 4. Quantum: KM configuration
// 5. Appearance: Theme, colors, fonts
```

### NotificationCenter

Toast notification display.

```jsx
// Props: none (uses store - notifications array)

// Features:
// - Success/Error/Warning/Info types
// - Auto-dismiss after 5 seconds
// - Manual dismiss button
// - Animated enter/exit

// Notification shape:
interface Notification {
  id: number;
  type: 'success' | 'error' | 'warning' | 'info';
  title: string;
  message: string;
  keyId?: string;
}
```

## 9.3 Common Components

### Modal

Reusable modal wrapper.

```jsx
// Props:
interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title?: string;
  size?: 'sm' | 'md' | 'lg' | 'xl' | 'full';
  children: React.ReactNode;
}

// Usage:
<Modal isOpen={showModal} onClose={() => setShowModal(false)} title="My Modal">
  <div>Content</div>
</Modal>
```

### Tooltip

Hover tooltip component.

```jsx
// Props:
interface TooltipProps {
  content: string;
  position?: 'top' | 'bottom' | 'left' | 'right';
  children: React.ReactNode;
}

// Usage:
<Tooltip content="Click to send" position="top">
  <button>Send</button>
</Tooltip>
```

### ContextMenu

Right-click context menu.

```jsx
// Props:
interface ContextMenuProps {
  items: Array<{
    label: string;
    icon?: Component;
    onClick?: () => void;
    shortcut?: string;
    separator?: boolean;
    disabled?: boolean;
  }>;
  children: React.ReactNode;
}

// Usage:
<ContextMenu items={[
  { label: 'Reply', icon: Reply, onClick: handleReply },
  { separator: true },
  { label: 'Delete', icon: Trash2, onClick: handleDelete },
]}>
  <EmailListItem />
</ContextMenu>
```

---

# 10. Styling Guide

## 10.1 Tailwind CSS Configuration

```javascript
// tailwind.config.js
module.exports = {
  content: ["./src/**/*.{js,jsx}"],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        // Primary palette (blue)
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
        },
        // Secondary palette (purple)
        secondary: {
          500: '#8b5cf6',
          600: '#7c3aed',
        },
        // Quantum security colors
        quantum: {
          secure: '#10b981',  // OTP - green
          aided: '#3b82f6',   // AES - blue
          hybrid: '#8b5cf6',  // PQC - purple
          none: '#6b7280',    // Standard - gray
        },
        // Surface colors
        surface: {
          light: '#f9fafb',
          dark: '#1f2937',
        },
      },
      fontFamily: {
        sans: ['Inter', 'Segoe UI', 'system-ui', 'sans-serif'],
        mono: ['Fira Code', 'Consolas', 'monospace'],
      },
      animation: {
        'fade-in': 'fadeIn 0.3s ease-in-out',
        'slide-up': 'slideUp 0.3s ease-out',
        'spin-slow': 'spin 2s linear infinite',
      },
    },
  },
}
```

## 10.2 CSS Custom Classes

```css
/* src/index.css */

@layer components {
  /* Buttons */
  .btn {
    @apply px-4 py-2 rounded-lg font-medium transition-all duration-200 
           focus:outline-none focus:ring-2 focus:ring-offset-2
           disabled:opacity-50 disabled:cursor-not-allowed;
  }
  
  .btn-primary {
    @apply btn bg-primary-600 text-white hover:bg-primary-700 
           focus:ring-primary-500;
  }
  
  .btn-secondary {
    @apply btn bg-gray-200 text-gray-800 hover:bg-gray-300 
           dark:bg-gray-700 dark:text-gray-200 dark:hover:bg-gray-600;
  }
  
  .btn-ghost {
    @apply btn bg-transparent hover:bg-gray-100 dark:hover:bg-gray-800;
  }
  
  /* Inputs */
  .input {
    @apply w-full px-4 py-2 border border-gray-300 rounded-lg 
           focus:outline-none focus:ring-2 focus:ring-primary-500 
           bg-white dark:bg-gray-800 dark:border-gray-600 dark:text-white
           placeholder-gray-400 transition-all duration-200;
  }
  
  /* Cards */
  .card {
    @apply bg-white dark:bg-gray-800 rounded-xl shadow-lg 
           border border-gray-200 dark:border-gray-700;
  }
  
  /* Sidebar */
  .sidebar-item {
    @apply flex items-center gap-3 px-4 py-2.5 rounded-lg 
           cursor-pointer transition-all duration-200
           hover:bg-gray-100 dark:hover:bg-gray-700
           text-gray-700 dark:text-gray-300;
  }
  
  .sidebar-item.active {
    @apply bg-primary-100 dark:bg-primary-900/30 
           text-primary-700 dark:text-primary-400 font-medium;
  }
  
  /* Badges */
  .badge {
    @apply inline-flex items-center justify-center px-2 py-0.5 
           text-xs font-medium rounded-full;
  }
  
  .badge-primary { @apply badge bg-primary-100 text-primary-700; }
  .badge-success { @apply badge bg-green-100 text-green-700; }
  .badge-warning { @apply badge bg-yellow-100 text-yellow-700; }
  .badge-danger { @apply badge bg-red-100 text-red-700; }
}

@layer utilities {
  /* Electron-specific */
  .drag-region { -webkit-app-region: drag; }
  .no-drag { -webkit-app-region: no-drag; }
  
  /* Hide scrollbar */
  .scrollbar-hide {
    -ms-overflow-style: none;
    scrollbar-width: none;
  }
  .scrollbar-hide::-webkit-scrollbar { display: none; }
}
```

## 10.3 Color Scheme Reference

### Light Mode

| Element | Color | Hex |
|---------|-------|-----|
| Background | White | `#ffffff` |
| Surface | Gray 50 | `#f9fafb` |
| Text Primary | Gray 900 | `#111827` |
| Text Secondary | Gray 500 | `#6b7280` |
| Border | Gray 200 | `#e5e7eb` |
| Primary | Blue 600 | `#2563eb` |
| Success | Green 500 | `#10b981` |
| Warning | Yellow 500 | `#f59e0b` |
| Error | Red 500 | `#ef4444` |

### Dark Mode

| Element | Color | Hex |
|---------|-------|-----|
| Background | Gray 900 | `#111827` |
| Surface | Gray 800 | `#1f2937` |
| Text Primary | Gray 100 | `#f3f4f6` |
| Text Secondary | Gray 400 | `#9ca3af` |
| Border | Gray 700 | `#374151` |
| Primary | Blue 500 | `#3b82f6` |
| Success | Green 400 | `#34d399` |
| Warning | Yellow 400 | `#fbbf24` |
| Error | Red 400 | `#f87171` |

## 10.4 Typography

```css
/* Font sizes */
--text-xs: 0.75rem;    /* 12px */
--text-sm: 0.875rem;   /* 14px */
--text-base: 1rem;     /* 16px - Body */
--text-lg: 1.125rem;   /* 18px */
--text-xl: 1.25rem;    /* 20px - H3 */
--text-2xl: 1.5rem;    /* 24px - H2 */
--text-3xl: 1.875rem;  /* 30px - H1 */

/* Font weights */
--font-normal: 400;
--font-medium: 500;
--font-semibold: 600;
--font-bold: 700;
```

## 10.5 Responsive Breakpoints

```javascript
// Tailwind default breakpoints
sm: '640px',   // Small devices
md: '768px',   // Medium devices
lg: '1024px',  // Large devices (min supported)
xl: '1280px',  // Extra large
'2xl': '1536px' // 2X Extra large
```

---

# 11. Security Features

## 11.1 Security Levels Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        Security Level Hierarchy                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  Level 1: Quantum Secure (OTP)                                          │
│  ══════════════════════════════                                         │
│  • Algorithm: One-Time Pad (XOR)                                        │
│  • Key Source: QKD-generated random bits                                │
│  • Key Usage: Single use, then destroyed                                │
│  • Security: Information-theoretic security (unbreakable)               │
│  • Use Case: Top secret communications                                  │
│                                                                          │
│  Level 2: Quantum-aided AES                                             │
│  ══════════════════════════════                                         │
│  • Algorithm: AES-256-GCM                                               │
│  • Key Source: QKD-generated keys                                       │
│  • Key Usage: Can be reused (with rotation)                             │
│  • Security: Computational security with quantum key                    │
│  • Use Case: Confidential business communications                       │
│                                                                          │
│  Level 3: Post-Quantum Cryptography (Hybrid)                            │
│  ═══════════════════════════════════════════                            │
│  • Algorithm: Kyber (ML-KEM) + AES-256                                  │
│  • Key Source: PQC key exchange                                         │
│  • Key Usage: Session-based                                             │
│  • Security: Resistant to quantum computer attacks                      │
│  • Use Case: Future-proof encryption                                    │
│                                                                          │
│  Level 4: Standard Encryption                                           │
│  ═════════════════════════════                                          │
│  • Algorithm: TLS 1.3 / Standard email encryption                       │
│  • Key Source: Traditional PKI                                          │
│  • Security: Standard transport encryption                              │
│  • Use Case: Non-sensitive communications                               │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## 11.2 Key Manager Integration

```javascript
// Key Manager API structure

// Get keys from KM
const getKeys = async (params) => {
  const response = await fetch(`${KM_ENDPOINT}/keys`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${API_KEY}`,
      'X-SAE-ID': SAE_ID,
    },
    body: JSON.stringify({
      count: params.count,
      size: params.size,        // Key size in bytes
      slave_SAE_ID: params.recipientId,
    }),
  });
  
  return response.json();
  // Returns: { keys: [{ key_ID, key, size, retrieved_at }] }
};

// Key lifecycle
const KEY_STATES = {
  AVAILABLE: 'available',  // Ready to use
  IN_USE: 'inUse',         // Currently encrypting
  USED: 'used',            // Consumed (OTP)
  EXPIRED: 'expired',      // Time-based expiration
};
```

## 11.3 Encryption Flow

```
┌──────────────────────────────────────────────────────────────────┐
│                     Email Encryption Flow                         │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│  1. User Composes Email                                          │
│     ↓                                                            │
│  2. Select Security Level                                        │
│     ↓                                                            │
│  3. Check Recipient Capability                                   │
│     ├── Quantum-capable? → Proceed with selected level           │
│     └── Not capable? → Fallback to Level 4 or warn user          │
│     ↓                                                            │
│  4. Request Key from KM (if Level 1-3)                           │
│     ├── Get key_ID and key material                              │
│     └── Mark key as IN_USE                                       │
│     ↓                                                            │
│  5. Encrypt Email Content                                        │
│     ├── Level 1: message XOR key                                 │
│     ├── Level 2: AES-256-GCM(key, message)                       │
│     └── Level 3: Kyber-KEM + AES                                 │
│     ↓                                                            │
│  6. Encrypt Attachments (same key or separate)                   │
│     ↓                                                            │
│  7. Package Encrypted Email                                      │
│     ├── Encrypted body                                           │
│     ├── Encrypted attachments                                    │
│     ├── key_ID reference (not the key!)                          │
│     └── Security metadata                                        │
│     ↓                                                            │
│  8. Send via SMTP                                                │
│     ↓                                                            │
│  9. Mark Key as USED (Level 1) or update status                  │
│                                                                   │
└──────────────────────────────────────────────────────────────────┘
```

## 11.4 Electron Security Settings

```javascript
// Secure BrowserWindow configuration
mainWindow = new BrowserWindow({
  webPreferences: {
    // Security: Disable Node.js in renderer
    nodeIntegration: false,
    
    // Security: Enable context isolation
    contextIsolation: true,
    
    // Security: Disable remote module
    enableRemoteModule: false,
    
    // Security: Use preload script
    preload: path.join(__dirname, 'preload.js'),
    
    // Security: Sandbox renderer
    sandbox: true,
  },
});

// Content Security Policy
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; 
               script-src 'self'; 
               style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
               font-src 'self' https://fonts.gstatic.com;
               img-src 'self' data: https:;">
```

## 11.5 Secure Storage

```javascript
// Using Electron's safeStorage for credentials
const { safeStorage } = require('electron');

// Store credential
const storeCredential = (key, value) => {
  if (safeStorage.isEncryptionAvailable()) {
    const encrypted = safeStorage.encryptString(JSON.stringify(value));
    // Store encrypted buffer
    storage.set(key, encrypted);
  }
};

// Retrieve credential
const retrieveCredential = (key) => {
  const encrypted = storage.get(key);
  if (encrypted && safeStorage.isEncryptionAvailable()) {
    return JSON.parse(safeStorage.decryptString(encrypted));
  }
  return null;
};
```

---

# 12. IPC Communication

## 12.1 IPC Architecture

```
┌────────────────────────────────────────────────────────────────────────┐
│                         IPC Communication                               │
├────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  Renderer Process                    Main Process                       │
│  ════════════════                    ════════════                       │
│                                                                         │
│  window.electronAPI.method()  ───►  ipcMain.handle('method')           │
│       (invoke)                           │                              │
│                                          ▼                              │
│                                    Process request                      │
│                                          │                              │
│       Result  ◄─────────────────────────┘                              │
│                                                                         │
│  ─────────────────────────────────────────────────────────────────────  │
│                                                                         │
│  ipcRenderer.on('event')     ◄───  mainWindow.webContents.send()       │
│       (listener)                        (push events)                   │
│                                                                         │
└────────────────────────────────────────────────────────────────────────┘
```

## 12.2 IPC Methods Reference

### Window Control

| Method | Direction | Description |
|--------|-----------|-------------|
| `window-minimize` | Renderer → Main | Minimize window |
| `window-maximize` | Renderer → Main | Toggle maximize |
| `window-close` | Renderer → Main | Close window |
| `window-is-maximized` | Invoke | Check if maximized |

### Authentication

| Method | Direction | Description |
|--------|-----------|-------------|
| `authenticate` | Invoke | Login with credentials |
| `logout` | Invoke | Logout and clear session |
| `get-app-version` | Invoke | Get application version |

### Key Manager

| Method | Direction | Description |
|--------|-----------|-------------|
| `km-connect` | Invoke | Connect to KM |
| `km-disconnect` | Invoke | Disconnect from KM |
| `km-get-keys` | Invoke | Fetch available keys |
| `km-request-keys` | Invoke | Request new keys |
| `km-key-status` | Invoke | Get key statistics |

### Email Operations

| Method | Direction | Description |
|--------|-----------|-------------|
| `fetch-emails` | Invoke | Get emails for folder |
| `send-email` | Invoke | Send encrypted email |
| `delete-email` | Invoke | Delete email |
| `move-email` | Invoke | Move to folder |

### Storage

| Method | Direction | Description |
|--------|-----------|-------------|
| `secure-store` | Invoke | Store encrypted data |
| `secure-retrieve` | Invoke | Retrieve encrypted data |
| `secure-delete` | Invoke | Delete stored data |
| `get-settings` | Invoke | Load app settings |
| `save-settings` | Invoke | Save app settings |

### Events (Main → Renderer)

| Event | Description |
|-------|-------------|
| `menu-action` | Menu item clicked |
| `new-email-received` | New email notification |
| `key-update` | Key status changed |
| `connection-status` | KM connection changed |

## 12.3 IPC Usage Examples

### Invoke Pattern (Request/Response)

```javascript
// Renderer (React component)
const sendEmail = async (emailData) => {
  try {
    const result = await window.electronAPI.sendEmail(emailData);
    if (result.success) {
      console.log('Email sent:', result.messageId);
    }
  } catch (error) {
    console.error('Send failed:', error);
  }
};

// Main process (ipc-handlers.js)
ipcMain.handle('send-email', async (event, emailData) => {
  // Validate
  if (!emailData.to || !emailData.subject) {
    return { success: false, error: 'Missing required fields' };
  }
  
  // Process
  const result = await emailService.send(emailData);
  
  return { success: true, messageId: result.id };
});
```

### Event Listener Pattern

```javascript
// Renderer (React component)
useEffect(() => {
  const cleanup = window.electronAPI.onMenuAction((action) => {
    switch (action) {
      case 'new-email':
        setShowCompose(true);
        break;
      case 'settings':
        setShowSettings(true);
        break;
    }
  });
  
  return cleanup; // Removes listener on unmount
}, []);

// Main process
Menu.buildFromTemplate([
  {
    label: 'New Email',
    click: () => mainWindow.webContents.send('menu-action', 'new-email'),
  },
]);
```

---

# 13. API Reference

## 13.1 Email Data Structures

### Email Object

```typescript
interface Email {
  id: string;                    // Unique identifier
  from: {
    name: string;
    email: string;
  };
  to: {
    name: string;
    email: string;
  };
  cc?: string[];
  bcc?: string[];
  subject: string;
  preview: string;               // First 100 chars
  body: string;                  // HTML content
  date: string;                  // ISO 8601
  read: boolean;
  starred: boolean;
  securityLevel: 1 | 2 | 3 | 4;
  keyId: string | null;          // QKD key reference
  hasAttachments: boolean;
  attachments: Attachment[];
}

interface Attachment {
  name: string;
  size: number;                  // Bytes
  mimeType: string;
  encrypted: boolean;
}
```

### Compose Email Data

```typescript
interface ComposeEmailData {
  to: string;
  cc?: string;
  bcc?: string;
  subject: string;
  body: string;
  securityLevel: 1 | 2 | 3 | 4;
  attachments: File[];
  replyTo?: string;              // Original email ID
  forwardFrom?: string;          // Original email ID
}
```

## 13.2 Key Data Structures

### Quantum Key

```typescript
interface QuantumKey {
  id: string;                    // e.g., "QK-2024-02-11-001"
  size: number;                  // Bytes
  retrievedAt: string;           // ISO 8601
  expiresAt: string;             // ISO 8601
  status: 'available' | 'inUse' | 'used' | 'expired';
}

interface KeyStats {
  availableCount: number;
  inUseCount: number;
  usedCount: number;
  expiredCount: number;
  lastSync: string | null;
}
```

## 13.3 Settings Structure

```typescript
interface Settings {
  general: {
    startOnBoot: boolean;
    minimizeToTray: boolean;
    showNotifications: boolean;
    playSound: boolean;
    language: 'en' | 'es' | 'fr' | 'de' | 'zh';
    autoCheckMail: number;       // Minutes, 0 = manual
  };
  
  security: {
    defaultSecurityLevel: 1 | 2 | 3 | 4;
    enforceQuantum: boolean;
    warnNonQuantum: boolean;
    blockNonQuantum: boolean;
    autoDeleteUsedKeys: boolean;
    warnLowKeys: boolean;
    lowKeyThreshold: number;
    keyRefreshInterval: number;  // Minutes
    maxKeyCache: number;
    encryptAttachments: boolean;
    scanAttachments: boolean;
    maxAttachmentSize: number;   // MB
  };
  
  appearance: {
    theme: 'light' | 'dark' | 'system';
    colorScheme: 'blue' | 'purple' | 'green' | 'red' | 'orange';
    uiFont: string;
    emailFont: string;
    fontSize: 'small' | 'medium' | 'large';
  };
  
  quantum: {
    primaryKm: {
      endpoint: string;
      saeId: string;
      apiKey: string;
    };
    backupKm: {
      enabled: boolean;
      endpoint: string;
      saeId: string;
    };
    connectionTimeout: number;   // Seconds
    retryAttempts: number;
    useTls13: boolean;
    verifySsl: boolean;
  };
}
```

## 13.4 API Response Formats

### Success Response

```typescript
interface SuccessResponse<T> {
  success: true;
  data?: T;
  message?: string;
}

// Example
{
  success: true,
  data: { messageId: "MSG-123456" },
  message: "Email sent successfully"
}
```

### Error Response

```typescript
interface ErrorResponse {
  success: false;
  error: string;
  code?: string;
  details?: Record<string, any>;
}

// Example
{
  success: false,
  error: "Key retrieval failed",
  code: "KM_CONNECTION_TIMEOUT",
  details: { timeout: 30000 }
}
```

---

# 14. Testing

## 14.1 Testing Strategy

```
┌────────────────────────────────────────────────────────────────┐
│                      Testing Pyramid                            │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│                        ╱╲                                       │
│                       ╱  ╲        E2E Tests                     │
│                      ╱────╲       (Spectron/Playwright)         │
│                     ╱      ╲                                    │
│                    ╱────────╲     Integration Tests             │
│                   ╱          ╲    (Component + Store)           │
│                  ╱────────────╲                                 │
│                 ╱              ╲   Unit Tests                   │
│                ╱────────────────╲  (Utils, Helpers, Store)      │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

## 14.2 Test Setup

```bash
# Install testing dependencies
npm install --save-dev @testing-library/react @testing-library/jest-dom jest
```

```javascript
// src/setupTests.js
import '@testing-library/jest-dom';

// Mock Electron API
window.electronAPI = {
  minimizeWindow: jest.fn(),
  maximizeWindow: jest.fn(),
  closeWindow: jest.fn(),
  sendEmail: jest.fn().mockResolvedValue({ success: true }),
  fetchEmails: jest.fn().mockResolvedValue({ success: true, emails: [] }),
  getKeys: jest.fn().mockResolvedValue({ success: true, keys: [] }),
  // ... other mocks
};
```

## 14.3 Unit Test Examples

### Testing Store Actions

```javascript
// src/store/__tests__/useStore.test.js
import useStore from '../useStore';

describe('useStore', () => {
  beforeEach(() => {
    // Reset store
    useStore.setState({
      isAuthenticated: false,
      user: null,
      emails: { inbox: [], sent: [], drafts: [], trash: [], starred: [] },
    });
  });

  describe('login', () => {
    it('should set isAuthenticated to true on successful login', async () => {
      const { login } = useStore.getState();
      
      const result = await login({ email: 'test@example.com', password: 'pass' });
      
      expect(result.success).toBe(true);
      expect(useStore.getState().isAuthenticated).toBe(true);
    });
  });

  describe('setSelectedFolder', () => {
    it('should update selected folder and clear selected email', () => {
      useStore.setState({ selectedEmail: { id: '123' } });
      
      const { setSelectedFolder } = useStore.getState();
      setSelectedFolder('sent');
      
      expect(useStore.getState().selectedFolder).toBe('sent');
      expect(useStore.getState().selectedEmail).toBeNull();
    });
  });
});
```

### Testing Components

```javascript
// src/components/EmailList/__tests__/EmailList.test.jsx
import { render, screen, fireEvent } from '@testing-library/react';
import EmailList from '../EmailList';
import useStore from '../../../store/useStore';

// Mock the store
jest.mock('../../../store/useStore');

describe('EmailList', () => {
  const mockEmails = [
    {
      id: '1',
      from: { name: 'Alice', email: 'alice@test.com' },
      subject: 'Test Email',
      preview: 'This is a test...',
      date: '2024-02-11T10:00:00Z',
      read: false,
      starred: false,
      securityLevel: 1,
    },
  ];

  beforeEach(() => {
    useStore.mockReturnValue({
      emails: { inbox: mockEmails },
      selectedFolder: 'inbox',
      selectedEmail: null,
      setSelectedEmail: jest.fn(),
      searchQuery: '',
      securityFilter: 'all',
      isLoading: false,
    });
  });

  it('renders email list items', () => {
    render(<EmailList />);
    
    expect(screen.getByText('Alice')).toBeInTheDocument();
    expect(screen.getByText('Test Email')).toBeInTheDocument();
  });

  it('calls setSelectedEmail when email is clicked', () => {
    const setSelectedEmail = jest.fn();
    useStore.mockReturnValue({
      ...useStore(),
      setSelectedEmail,
    });
    
    render(<EmailList />);
    fireEvent.click(screen.getByText('Test Email'));
    
    expect(setSelectedEmail).toHaveBeenCalledWith(mockEmails[0]);
  });
});
```

## 14.4 Running Tests

```bash
# Run all tests
npm test

# Run tests with coverage
npm test -- --coverage

# Run specific test file
npm test -- EmailList.test.jsx

# Run tests in watch mode
npm test -- --watch
```

---

# 15. Build & Deployment

## 15.1 Build Commands

```bash
# Build React app
npm run build

# Build Electron distributables
npm run electron-build

# Build for specific platform
npx electron-builder --win    # Windows
npx electron-builder --mac    # macOS
npx electron-builder --linux  # Linux
```

## 15.2 Build Configuration

```javascript
// package.json - electron-builder config
{
  "build": {
    "appId": "com.qumail.app",
    "productName": "QuMail",
    "copyright": "Copyright © 2024",
    
    "directories": {
      "output": "dist",
      "buildResources": "build-resources"
    },
    
    "files": [
      "build/**/*",
      "electron/**/*",
      "node_modules/**/*"
    ],
    
    "extraResources": [
      {
        "from": "assets/",
        "to": "assets/"
      }
    ],
    
    "mac": {
      "category": "public.app-category.productivity",
      "icon": "build-resources/icon.icns",
      "hardenedRuntime": true,
      "gatekeeperAssess": false,
      "entitlements": "build-resources/entitlements.mac.plist"
    },
    
    "win": {
      "target": ["nsis", "portable"],
      "icon": "build-resources/icon.ico"
    },
    
    "linux": {
      "target": ["AppImage", "deb"],
      "icon": "build-resources/icons",
      "category": "Office"
    },
    
    "nsis": {
      "oneClick": false,
      "allowToChangeInstallationDirectory": true,
      "installerIcon": "build-resources/icon.ico",
      "uninstallerIcon": "build-resources/icon.ico"
    }
  }
}
```

## 15.3 Build Output

```
dist/
├── QuMail-1.0.0.exe              # Windows installer
├── QuMail-1.0.0-portable.exe     # Windows portable
├── QuMail-1.0.0.dmg              # macOS installer
├── QuMail-1.0.0.AppImage         # Linux AppImage
├── qumail_1.0.0_amd64.deb        # Debian package
└── latest.yml                     # Auto-update manifest
```

## 15.4 Code Signing

### macOS

```bash
# Set environment variables
export CSC_LINK=path/to/certificate.p12
export CSC_KEY_PASSWORD=certificate_password

# Build with signing
npm run electron-build
```

### Windows

```bash
# Set environment variables
export CSC_LINK=path/to/certificate.pfx
export CSC_KEY_PASSWORD=certificate_password

# Build with signing
npm run electron-build
```

## 15.5 Auto-Update Configuration

```javascript
// electron/main.js
const { autoUpdater } = require('electron-updater');

// Check for updates on startup
app.whenReady().then(() => {
  autoUpdater.checkForUpdatesAndNotify();
});

// Handle update events
autoUpdater.on('update-available', () => {
  mainWindow.webContents.send('update-available');
});

autoUpdater.on('update-downloaded', () => {
  mainWindow.webContents.send('update-downloaded');
  // Prompt user to restart
});
```

---

# 16. Troubleshooting

## 16.1 Common Issues

### Installation Issues

| Problem | Solution |
|---------|----------|
| `npm install` fails | Try `npm install --legacy-peer-deps` |
| Native module errors | Rebuild with `npm rebuild` |
| Permission denied | Run terminal as administrator |

### Development Issues

| Problem | Solution |
|---------|----------|
| Blank white screen | Check browser console for errors (F12) |
| React not loading | Ensure `npm run react-start` is running on port 3000 |
| Electron not connecting | Wait for React to fully start before running Electron |
| Tailwind not working | Run `npx tailwindcss init -p` and restart |

### Build Issues

| Problem | Solution |
|---------|----------|
| Build fails | Clear `node_modules` and reinstall |
| App not starting | Check `dist` folder for output |
| Missing icons | Add icons to `build-resources` folder |

## 16.2 Debug Mode

```javascript
// Enable verbose logging
// In electron/main.js

const isDev = process.env.NODE_ENV === 'development';

if (isDev) {
  // Open DevTools
  mainWindow.webContents.openDevTools();
  
  // Log all IPC messages
  ipcMain.on('*', (event, ...args) => {
    console.log('IPC:', event, args);
  });
}
```

## 16.3 Log Locations

| Platform | Location |
|----------|----------|
| Windows | `%APPDATA%\QuMail\logs\` |
| macOS | `~/Library/Logs/QuMail/` |
| Linux | `~/.config/QuMail/logs/` |

## 16.4 Error Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `KM_CONNECTION_TIMEOUT` | KM server not responding | Check endpoint URL and network |
| `KM_AUTH_FAILED` | Invalid API key or SAE ID | Verify credentials |
| `KEY_RETRIEVAL_FAILED` | Cannot get keys from KM | Check key availability |
| `ENCRYPTION_FAILED` | Encryption process error | Check key validity |
| `EMAIL_SEND_FAILED` | SMTP error | Verify email configuration |

---

# 17. Contributing

## 17.1 Development Workflow

```
1. Fork the repository
2. Create feature branch: git checkout -b feature/my-feature
3. Make changes
4. Run tests: npm test
5. Commit: git commit -m "feat: add new feature"
6. Push: git push origin feature/my-feature
7. Create Pull Request
```

## 17.2 Commit Convention

```
<type>(<scope>): <description>

Types:
- feat: New feature
- fix: Bug fix
- docs: Documentation
- style: Formatting
- refactor: Code restructuring
- test: Adding tests
- chore: Maintenance

Examples:
- feat(compose): add security level selector
- fix(email-list): correct date formatting
- docs(readme): update installation steps
```

## 17.3 Code Style

```javascript
// Use functional components
const MyComponent = ({ prop1, prop2 }) => {
  // Hooks at the top
  const [state, setState] = useState();
  const store = useStore();
  
  // Effects after hooks
  useEffect(() => {
    // ...
  }, []);
  
  // Handlers
  const handleClick = () => {
    // ...
  };
  
  // Render
  return (
    <div className="...">
      {/* ... */}
    </div>
  );
};

export default MyComponent;
```

## 17.4 Pull Request Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Manual testing performed

## Screenshots (if applicable)

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
```

---

# Appendix

## A. Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+N` | New Email |
| `Ctrl+R` | Reply |
| `Ctrl+Shift+R` | Reply All |
| `Ctrl+F` | Forward |
| `Ctrl+S` | Save Draft |
| `Ctrl+Enter` | Send Email |
| `Delete` | Delete Email |
| `Ctrl+K` | Key Manager |
| `Ctrl+,` | Settings |
| `F5` | Refresh |
| `Ctrl+Q` | Quit |

## B. Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `REACT_APP_NAME` | App name | QuMail |
| `REACT_APP_VERSION` | Version | 1.0.0 |
| `REACT_APP_KM_ENDPOINT` | KM API URL | - |
| `REACT_APP_MOCK_DATA` | Use mock data | true |

## C. External Resources

- [Electron Documentation](https://www.electronjs.org/docs)
- [React Documentation](https://react.dev)
- [Zustand Documentation](https://zustand-demo.pmnd.rs/)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [Framer Motion Documentation](https://www.framer.com/motion/)
- [Recharts Documentation](https://recharts.org/)

---

**Document Version:** 1.0.0  
**Last Updated:** February 2024  
**Maintainer:** QuMail Development Team