# MapIn - Decentralized Pin Sharing

A simple, decentralized web application for sharing map pins using OpenStreetMap and Waku network.

## Features

- **Interactive Map**: Click anywhere on the map to place a pin
- **Decentralized Synchronization**: Pins are shared across all users via Waku's reliable channel
- **Local Persistence**: Pins are stored in your browser's LocalStorage
- **Real-time Updates**: See pins from other users appear in real-time
- **Connection Status**: Visual indicator showing Waku network connection status
- **Sync Status**: Real-time display of message synchronization with received/missing/lost counts

## Quick Start

### 1. Run a Local Web Server

The app needs to be served over HTTP. Use any of these methods:

**Python 3:**
```bash
python -m http.server 8000
```

**Python 2:**
```bash
python -m SimpleHTTPServer 8000
```

**Node.js:**
```bash
npx http-server -p 8000
```

### 2. Open in Browser

Navigate to `http://localhost:8000` in your web browser.

### 3. Start Pinning!

- Click anywhere on the map to add a pin
- Your pins will appear instantly on your map
- Pins will sync to other users via the Waku network
- Open multiple browser tabs to test synchronization locally

## How It Works

### Technology Stack

- **Leaflet.js**: Renders the interactive OpenStreetMap
- **Waku SDK v0.0.37**: Provides decentralized messaging using Light Node and Reliable Channels
- **Protobuf.js**: Encodes/decodes messages for efficient network transmission
- **Vanilla JavaScript**: No frameworks, just pure ES6+ modules
- **LocalStorage**: Caches pins locally for persistence

### Architecture

1. **Pin Creation**: User clicks map → Pin created with coordinates and timestamp
2. **Local Storage**: Pin saved to browser LocalStorage
3. **Encoding**: Pin encoded using Protobuf for efficient transmission
4. **Broadcasting**: Encoded pin sent to Waku network via reliable channel
5. **Receiving**: Other users receive pin messages via event listeners
6. **Decoding**: Protobuf message decoded to pin object
7. **Deduplication**: Pins are validated and deduplicated by coordinates + timestamp
8. **Display**: Received pins are added to map and LocalStorage

### Pin Data Structure

```javascript
{
  latitude: number,    // Pin latitude
  longitude: number,   // Pin longitude
  timestamp: number    // Unix timestamp (milliseconds)
}
```

## Testing Multi-User Synchronization

### Local Testing (Same Computer)

1. Start the web server
2. Open `http://localhost:8000` in multiple browser tabs or windows
3. Add pins in one tab and watch them appear in the others

### Remote Testing (Different Computers)

1. Deploy to any static hosting service (GitHub Pages, Netlify, Vercel, etc.)
2. Share the URL with others
3. Add pins and see them sync across all users

## Browser Console

Check the browser console (F12) to see:
- Waku connection status
- Pin broadcast confirmations
- Incoming pin messages
- Sync status updates (syncing/synced with message counts)
- Any errors or warnings

### Sync Status Indicators

The info panel displays sync status in real-time:
- **Syncing** (orange ●): Retrieving missing messages from the network
- **Synced** (green ●): All messages synchronized
- **Warning** (red ●): Some messages permanently lost

Message counts show: `X received, Y missing` (when syncing) or `X received, Y lost` (when synced)

### Useful Console Commands

**View all pins:**
```javascript
JSON.parse(localStorage.getItem('mapin_pins'))
```

**Clear all pins:**
```javascript
localStorage.removeItem('mapin_pins')
location.reload()
```

## Deployment

Since this is a single HTML file with no build process, you can deploy it anywhere:

### GitHub Pages
1. Push to GitHub repository
2. Enable GitHub Pages in repository settings
3. Select the branch and root folder
4. Access via `https://username.github.io/repository-name/`

### Netlify
```bash
# Install Netlify CLI
npm install -g netlify-cli

# Deploy
netlify deploy --prod
```

### Vercel
```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel --prod
```

## Configuration

You can customize these settings in `index.html`:

**Default Map Center** (line ~173):
```javascript
map = L.map('map').setView([51.505, -0.09], 13);
//                          [latitude, longitude], zoom
```

**Waku Content Topic** (line ~156):
```javascript
const CONTENT_TOPIC = '/mapin/1/pins/proto';
```

**Waku Channel Name** (line ~157):
```javascript
const CHANNEL_NAME = 'mapin-global';
```

**LocalStorage Key** (line ~155):
```javascript
const STORAGE_KEY = 'mapin_pins';
```

## Known Limitations

- No authentication - anyone can add pins
- No pin deletion - pins persist until LocalStorage is cleared
- No rate limiting - users can add unlimited pins
- LocalStorage has ~5-10MB limit (browser dependent)
- Requires Waku network connectivity

## Troubleshooting

**Map not loading:**
- Check internet connection (Leaflet tiles require network access)
- Check browser console for errors

**Waku not connecting:**
- Check browser console for connection errors
- Waku may take 10-30 seconds to establish connection
- Some networks/firewalls may block WebSocket connections

**Pins not syncing:**
- Ensure Waku status shows "Connected"
- Check browser console for error messages
- Open browser console in both tabs to verify message flow

**LocalStorage full:**
```javascript
// Clear old pins
localStorage.removeItem('mapin_pins')
location.reload()
```

## Contributing

This is a simple single-file project. Feel free to:
- Fork and modify for your use case
- Add features (pin labels, user IDs, authentication, etc.)
- Improve the UI/UX
- Optimize Waku integration

## License

MIT License - feel free to use and modify as you wish.
