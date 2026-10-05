# 📈 LiveStocker

**LiveStocker** is a browser-based stock market tracking application that allows users to search for stocks, view real-time market prices, and maintain a personalized watchlist.

The project is built as a browser extension with a Node.js/Express backend that securely communicates with the **Shoonya API** and provides real-time stock updates through WebSockets.

---

## 🎥 Project Demo

Watch the LiveStocker browser extension in action:

👉 **[View the LiveStocker Demo Video](https://jumpshare.com/folder/IVE0dhKfnUUrO01tl89g)**

The demo showcases:

- 🔎 Searching for stocks
- 📊 Viewing real-time stock prices
- ⭐ Adding stocks to the watchlist
- 📈 Monitoring live price changes
- 🔄 Receiving real-time updates through WebSockets

---

## 🚀 Features

### 🔎 Stock Search

Users can search for stocks and companies directly from the extension.

The frontend sends the search query to the backend, which communicates with the Shoonya Search Scrip API and returns matching instruments.

```text
User enters stock name
        ↓
Frontend
        ↓
Express Backend
        ↓
Shoonya Search API
        ↓
Matching stocks
        ↓
Frontend displays results
```

---

### 📊 Real-Time Stock Prices

Live stock prices are received through a WebSocket connection.

The application connects to:

```text
wss://api.shoonya.com/NorenWSTP/
```

After authentication, the application subscribes to selected stock tokens and receives live market updates.

The application displays:

* Current price
* Price change
* Percentage change
* Stock symbol
* Exchange information

---

### ⭐ Watchlist

Users can add stocks to their personal watchlist.

The watchlist stores:

* Stock symbol
* Stock token
* Latest price
* Price change
* Percentage change

Watchlist data is persisted using the browser's `localStorage`, allowing the list to remain available when the extension is reopened.

Users can also remove stocks from the watchlist.

---

### 🔄 Live Watchlist Updates

When the watchlist is opened, the application creates a WebSocket connection and subscribes to all saved stock tokens.

```text
Watchlist
    │
    ▼
Saved Stock Tokens
    │
    ▼
Shoonya WebSocket
    │
    ▼
Live Market Data
    │
    ▼
Update Watchlist
```

This allows prices to update without manually refreshing the page.

---

### 🔐 API Authentication

The backend handles authentication with the Shoonya API.

The backend generates a TOTP-based authentication code and uses environment variables for sensitive credentials.

This prevents sensitive Shoonya credentials from being directly exposed in the browser extension.

---

# 🏗️ Architecture

The project follows a simple client-server architecture:

```text
┌─────────────────────────────┐
│      Browser Extension      │
│                             │
│  HTML + CSS + JavaScript    │
│                             │
│  • Search                   │
│  • Stock Details            │
│  • Watchlist                │
│  • UI interactions          │
└──────────────┬──────────────┘
               │
               │ HTTP API
               ▼
┌─────────────────────────────┐
│      Express Backend        │
│                             │
│  • Authentication           │
│  • TOTP generation          │
│  • API key validation       │
│  • Stock search proxy       │
└──────────────┬──────────────┘
               │
               │ HTTPS
               ▼
┌─────────────────────────────┐
│        Shoonya API          │
│                             │
│  • Authentication           │
│  • Stock search             │
│  • Market data              │
└─────────────────────────────┘

               │
               │ WebSocket
               ▼
        Live Market Updates
```

---

# 🛠️ Tech Stack

## Frontend

* HTML
* CSS
* JavaScript
* Browser Extension APIs
* WebSocket

## Backend

* Node.js
* Express.js
* CORS
* JavaScript
* `dotenv`
* `jsotp`

## Market Data

* Shoonya API
* Shoonya WebSocket API

## Storage

* Browser `localStorage`

---

# 📂 Project Structure

The repository contains the browser extension frontend and backend service.

```text
LiveStocker-Extension/
│
├── LiveStocker-Extension-Frontend/
│   │
│   ├── icons/
│   │
│   ├── manifest.json
│   ├── popup.html
│   ├── popup.css
│   └── popup.js
│
└── livestockerserve-backend/
    │
    ├── server.js
    ├── package.json
    ├── package-lock.json
    ├── jsOTP.min.js
    └── vercel.json
```

---

# 🧩 Frontend

The browser extension frontend is responsible for the user interface and interaction.

### `popup.html`

Defines the extension popup interface.

### `popup.css`

Contains styling for the extension UI.

### `popup.js`

Contains the primary application logic, including:

* Stock searching
* Stock selection
* Watchlist management
* WebSocket connections
* Live price updates
* UI state management
* Local storage handling

### `manifest.json`

Defines the browser extension configuration and permissions.

### `icons/`

Contains the extension icons.

---

# ⚙️ Backend

The backend is implemented using **Node.js and Express**.

The backend acts as an intermediary between the browser extension and Shoonya.

## Authentication Endpoint

```text
POST /api/get-token
```

This endpoint:

1. Generates a TOTP using the configured secret.
2. Builds the Shoonya authentication request.
3. Sends the request to Shoonya.
4. Receives the session token.
5. Returns the required authentication information to the frontend.

---

## Stock Search Endpoint

```text
POST /api/search-scrip
```

The frontend sends:

```json
{
  "query": "RELIANCE"
}
```

The backend then requests matching instruments from the Shoonya API and returns the response to the frontend.

---

# 🔐 Security

The backend uses environment variables for sensitive configuration.

Example variables include:

```text
MY_SECRET_KEY
totp
uid
pwd1
vc
appkey
imei
source
apkversion
```

These values should **never be committed to GitHub**.

Create a `.env` file locally:

```env
MY_SECRET_KEY=your_api_key
totp=your_totp_secret
uid=your_user_id
pwd1=your_password
vc=your_vendor_code
appkey=your_app_key
imei=your_device_id
source=your_source
apkversion=your_api_version
```

> ⚠️ Never upload your `.env` file or expose Shoonya credentials in frontend JavaScript.

---

# 💾 Watchlist Data

The watchlist is stored locally in the browser.

The application uses:

```javascript
localStorage
```

The saved watchlist uses the key:

```text
watchlistTemp
```

Individual stock information is stored using keys such as:

```text
stock-{token}
```

This allows the extension to persist the user's selected stocks between sessions.

---

# 📡 WebSocket Data Flow

When a user selects a stock, the application creates a WebSocket connection:

```text
Browser Extension
       │
       ▼
Shoonya WebSocket
       │
       ▼
Authentication
       │
       ▼
Subscribe to Stock Token
       │
       ▼
Live Price Updates
       │
       ▼
Update UI
```

For multiple watchlist stocks, the application subscribes to the corresponding stock tokens and updates the stored market information when new data arrives.

---

# 🔄 Stock Selection Flow

```text
Search Stock
     │
     ▼
Search API
     │
     ▼
Display Results
     │
     ▼
Select Stock
     │
     ▼
Open WebSocket
     │
     ▼
Receive Live Data
     │
     ▼
Display Price
     │
     ▼
Add to Watchlist
```

---

# ▶️ Running the Backend

Navigate to the backend directory:

```bash
cd livestockerserve-backend
```

Install dependencies:

```bash
npm install
```

Create your `.env` file with the required credentials.

Start the server:

```bash
node server.js
```

The server runs on:

```text
http://localhost:3000
```

unless another port is configured through the `PORT` environment variable.

---

# 🧪 Development

During development, the frontend communicates with the backend through the configured API endpoint.

The backend is responsible for communicating with Shoonya, while the frontend focuses on displaying and managing the stock information.

---

# 🌐 Browser Extension

The frontend can be loaded as an unpacked browser extension during development.

General workflow:

```text
Browser
   │
   ▼
Extensions
   │
   ▼
Developer Mode
   │
   ▼
Load Unpacked
   │
   ▼
Select LiveStocker-Extension-Frontend
```

The exact steps depend on the browser being used.

---

# 📈 Example Use Case

A typical user workflow looks like this:

1. Open the LiveStocker browser extension.
2. Search for a company or stock symbol.
3. Select the desired stock.
4. View its latest market price.
5. View the current price change and percentage change.
6. Add the stock to the watchlist.
7. Open the watchlist later to monitor multiple stocks.
8. Receive live updates through the WebSocket connection.

---

# 🔮 Future Improvements

Potential improvements include:

* 📊 Interactive price charts
* 📈 Historical market data
* 🔔 Price alerts
* 📱 Improved responsive UI
* 🧠 Advanced stock filtering
* ⭐ Improved watchlist management
* 🔄 Automatic WebSocket reconnection
* ⚡ Better error and connection handling
* 🔐 Improved authentication/session management
* 📉 Additional market indicators

---

# ⚠️ Disclaimer

LiveStocker is a stock-market data tracking project.

The application is intended for educational and informational purposes and does not constitute financial or investment advice.

Market data may be delayed, unavailable, or subject to third-party API limitations.

Users should perform their own research before making any investment decisions.

---

# 👨‍💻 Author

**Prajwal**

GitHub: [@praxjt](https://github.com/praxjt)

---

# ⭐ Support

If you find the project useful or interesting, consider giving the repository a ⭐ on GitHub.
