# Detailed Frontend Plan - Erweitertes Tic-Tac-Toe Framework

## 1. Technologie-Stack und Architektur-Entscheidungen

### 1.1 Grundprinzipien
- **KISS-Prinzip**: Vermeidung komplexer Frameworks wie React, Vue.js oder Angular
- **Vanilla JavaScript**: Maximale Kompatibilität und minimale Dependencies
- **Progressive Enhancement**: Funktionalität ohne JavaScript als Basis, dann Enhancement
- **Responsive Design**: Mobile-First Ansatz mit CSS Grid und Flexbox

### 1.2 Kern-Technologien
```
Frontend-Stack:
├── HTML5 (semantische Struktur)
├── CSS3 (Grid, Flexbox, Custom Properties)
├── Vanilla JavaScript (ES6+)
├── Canvas API (Spielfeld-Rendering)
├── WebSocket API (Echtzeit-Kommunikation)
└── localStorage API (Session-Management)
```

### 1.3 2D Game Engine Evaluation

Nach Analyse verschiedener leichtgewichtiger Optionen:

**Empfehlung: Konva.js**
- **Vorteile**: 
  - Leichtgewichtig (~87KB gzipped)
  - Canvas-basiert mit High-Level API
  - Excellent Performance für 2D
  - Layer-System für komplexe Szenen
  - Event-Handling auf Canvas-Objekten
  - Animation-System integriert
  - TypeScript Unterstützung verfügbar
- **Nachteile**: 
  - Zusätzliche Dependency (akzeptabel für die Komplexität)

**Alternative: Pure Canvas API**
- **Vorteile**: Keine Dependencies, maximale Kontrolle
- **Nachteile**: Mehr Boilerplate-Code, komplexeres Event-Handling

**Entscheidung**: Konva.js für die erste Implementation, mit fallback auf Pure Canvas API falls Performance-Probleme auftreten.

### 1.4 Architektur-Pattern

**Module Pattern mit ES6 Modules:**
```javascript
// Struktur:
src/
├── modules/
│   ├── game-engine/     # Konva.js Wrapper + Game Logic
│   ├── websocket/       # WebSocket Kommunikation
│   ├── session/         # Token + localStorage Management
│   ├── ui/              # UI Components
│   └── screens/         # Screen Controller
├── styles/
│   ├── base.css         # Reset, Variables, Typography
│   ├── components.css   # Wiederverwendbare Komponenten
│   └── screens.css      # Screen-spezifische Styles
└── assets/
    ├── images/          # Sprites, Icons
    └── sounds/          # Audio Effects (optional)
```

## 2. Datenaustausch und Backend-Integration

### 2.1 WebSocket Kommunikations-Layer

**WebSocket Manager Klasse:**
```javascript
class WebSocketManager {
    constructor() {
        this.socket = null;
        this.reconnectAttempts = 0;
        this.maxReconnectAttempts = 5;
        this.reconnectDelay = 1000; // Exponential backoff
        this.messageQueue = [];
        this.listeners = new Map();
    }
    
    connect(token) { /* Verbindung mit automatischem Reconnect */ }
    sendMessage(action, data) { /* Queue bei Disconnect */ }
    addListener(type, callback) { /* Event-basierte Message-Behandlung */ }
}
```

### 2.2 State Management

**Zentraler State Store (ohne Framework):**
```javascript
class GameStateManager {
    constructor() {
        this.currentScreen = 'start-menu';
        this.gameState = null;
        this.playerInfo = null;
        this.lobbyData = null;
        this.observers = new Map();
    }
    
    updateState(newState) {
        // Immutable State Updates
        // Observer Notification
    }
}
```

### 2.3 Datenmodell-Mapping

**Backend-Frontend Mapping:**
```javascript
// Backend GameState -> Frontend Rendering Data
class DataTransformer {
    static transformGameState(backendState) {
        return {
            grid: this.transformGrid(backendState.global_grid),
            players: this.transformPlayers(backendState.active_players),
            scores: backendState.scores,
            currentPlayer: backendState.phase_data.current_player_index,
            availableActions: backendState.available_actions,
            visualEffects: this.extractEffectData(backendState.active_modes)
        };
    }
    
    static transformGrid(globalGrid) {
        // Konvertierung der graph-basierten Backend-Struktur
        // in render-freundliche Frontend-Struktur
    }
}
```

## 3. Screen-Design und UI-Komponenten

### 3.1 Screen Architecture

**Basis Screen Controller:**
```javascript
class BaseScreen {
    constructor(containerId) {
        this.container = document.getElementById(containerId);
        this.isActive = false;
    }
    
    async show() { /* Fade-in, Animation */ }
    async hide() { /* Fade-out, Cleanup */ }
    handleResize() { /* Responsive Updates */ }
}
```

### 3.2 Start Menu Screen

**HTML Struktur:**
```html
<div id="start-menu" class="screen">
    <header class="game-title">
        <h1>Erweitertes Tic-Tac-Toe</h1>
        <p class="subtitle">Modulares Game Framework</p>
    </header>
    
    <main class="start-menu-content">
        <form id="player-form" class="player-setup">
            <label for="player-name">Spielername:</label>
            <input type="text" id="player-name" maxlength="20" required>
            <button type="submit">Spiel starten</button>
        </form>
        
        <div class="returning-player" style="display: none;">
            <p>Willkommen zurück, <span id="current-player-name"></span>!</p>
            <button id="continue-button">Weiter zur Lobby</button>
            <button id="change-name-button">Namen ändern</button>
        </div>
    </main>
</div>
```

**CSS Design:**
```css
.screen {
    display: none;
    min-height: 100vh;
    padding: 2rem;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.screen.active {
    display: flex;
    flex-direction: column;
    animation: fadeIn 0.5s ease-in-out;
}

.game-title {
    text-align: center;
    color: white;
    margin-bottom: 3rem;
}

.player-setup {
    max-width: 400px;
    margin: 0 auto;
    background: rgba(255, 255, 255, 0.1);
    padding: 2rem;
    border-radius: 10px;
    backdrop-filter: blur(10px);
}
```

**JavaScript Logic:**
```javascript
class StartMenuScreen extends BaseScreen {
    constructor() {
        super('start-menu');
        this.sessionManager = new SessionManager();
        this.initializeElements();
        this.bindEvents();
    }
    
    async show() {
        await super.show();
        const existingPlayer = this.sessionManager.getPlayer();
        if (existingPlayer) {
            this.showReturningPlayer(existingPlayer);
        } else {
            this.showNewPlayerForm();
        }
    }
    
    handlePlayerSubmit(event) {
        // Player Registration via WebSocket
        // Token Storage in localStorage
        // Transition to Lobby
    }
}
```

### 3.3 Lobby Screen

**HTML Struktur:**
```html
<div id="lobby" class="screen">
    <header class="lobby-header">
        <h2>Spiel-Lobby</h2>
        <div class="player-info">
            <span id="lobby-player-name"></span>
            <button id="change-name-lobby">Namen ändern</button>
        </div>
    </header>
    
    <main class="lobby-content">
        <section class="game-actions">
            <button id="create-game-btn" class="primary-btn">Neues Spiel erstellen</button>
            <button id="refresh-games-btn" class="secondary-btn">Aktualisieren</button>
        </section>
        
        <section class="available-games">
            <h3>Verfügbare Spiele</h3>
            <div id="games-list" class="games-grid">
                <!-- Dynamisch generiert -->
            </div>
        </section>
    </main>
</div>
```

**Game Card Template:**
```html
<template id="game-card-template">
    <div class="game-card" data-game-id="">
        <div class="game-info">
            <h4 class="game-creator"></h4>
            <p class="game-config"></p>
            <div class="game-status">
                <span class="player-count"></span>
                <span class="status-badge"></span>
            </div>
        </div>
        <button class="join-btn">Beitreten</button>
    </div>
</template>
```

**CSS Grid Layout:**
```css
.games-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 1rem;
    margin-top: 1rem;
}

.game-card {
    background: white;
    border-radius: 8px;
    padding: 1rem;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    transition: transform 0.2s ease;
}

.game-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 16px rgba(0,0,0,0.15);
}
```

### 3.4 Game Creation Screen

**Dynamische Konfiguration basierend auf Backend:**
```javascript
class GameCreationScreen extends BaseScreen {
    constructor() {
        super('game-creation');
        this.gameOptions = null;
        this.selectedConfig = {
            field_variant: 'rectangular_3x3',
            activation_mode: 'spiral',
            scoring_modes: ['line_count'],
            turn_mode: 'alternating',
            win_condition: 'most_points',
            special_rules: []
        };
    }
    
    async show() {
        await super.show();
        await this.loadGameOptions();
        this.renderConfigurationForm();
    }
    
    async loadGameOptions() {
        // WebSocket Request: get_game_options
        // Dynamic UI Generation based on backend response
    }
    
    renderConfigurationForm() {
        // Generiere Formulare für jede Konfigurationskategorie
        // Mit Kompatibilitäts-Validierung
    }
}
```

**HTML Form Structure:**
```html
<div id="game-creation" class="screen">
    <header>
        <h2>Neues Spiel erstellen</h2>
        <button id="back-to-lobby">← Zurück zur Lobby</button>
    </header>
    
    <form id="game-config-form" class="config-form">
        <!-- Dynamisch generiert basierend auf Backend-Optionen -->
        <fieldset class="config-section">
            <legend>Spielfeld-Variante</legend>
            <div id="field-variants" class="option-grid"></div>
        </fieldset>
        
        <fieldset class="config-section">
            <legend>Aktivierungs-Modus</legend>
            <div id="activation-modes" class="option-grid"></div>
        </fieldset>
        
        <!-- Weitere Sektionen... -->
        
        <div class="config-preview">
            <h4>Konfiguration-Vorschau</h4>
            <div id="config-summary"></div>
        </div>
        
        <div class="player-config">
            <label for="player-symbol">Dein Symbol:</label>
            <select id="player-symbol">
                <option value="X">X</option>
                <option value="O">O</option>
                <option value="★">★</option>
                <option value="♦">♦</option>
            </select>
        </div>
        
        <button type="submit" class="create-btn">Spiel erstellen</button>
    </form>
</div>
```

### 3.5 Game Board Screen

**Canvas-basierte Rendering-Architektur:**
```html
<div id="game-board" class="screen">
    <header class="game-header">
        <div class="player-info">
            <div class="player active" id="player-1">
                <span class="name"></span>
                <span class="symbol"></span>
                <span class="score"></span>
            </div>
            <div class="turn-indicator">Aktueller Spieler</div>
            <div class="player" id="player-2">
                <span class="name"></span>
                <span class="symbol"></span>
                <span class="score"></span>
            </div>
        </div>
        
        <div class="game-status">
            <span id="active-modes"></span>
            <button id="pause-game">Pause</button>
        </div>
    </header>
    
    <main class="game-container">
        <div id="game-canvas-container">
            <canvas id="game-canvas"></canvas>
        </div>
        
        <aside class="game-sidebar">
            <div class="move-history">
                <h4>Letzte Züge</h4>
                <ul id="moves-list"></ul>
            </div>
            
            <div class="active-effects">
                <h4>Aktive Effekte</h4>
                <div id="effects-display"></div>
            </div>
        </aside>
    </main>
    
    <!-- Connection Loss UI -->
    <div id="connection-overlay" class="overlay" style="display: none;">
        <div class="overlay-content">
            <h3>Verbindung unterbrochen</h3>
            <p>Versuche Wiederverbindung...</p>
            <div class="loading-spinner"></div>
        </div>
    </div>
    
    <!-- Pause Game UI -->
    <div id="pause-overlay" class="overlay" style="display: none;">
        <div class="overlay-content">
            <h3>Spiel pausiert</h3>
            <p id="pause-message"></p>
            <div class="pause-actions">
                <button id="resume-game">Fortsetzen</button>
                <button id="abort-game" class="danger">Spiel beenden</button>
            </div>
        </div>
    </div>
</div>
```

## 4. Canvas Rendering-System

### 4.1 Adaptive Rendering-Engine

**Grid Renderer Klasse:**
```javascript
class GridRenderer {
    constructor(stage, layer) {
        this.stage = stage;
        this.layer = layer;
        this.cellSize = 50;
        this.gridPadding = 10;
        this.subgridSpacing = 20;
    }
    
    renderGlobalGrid(gridData) {
        // Dynamische Berechnung basierend auf Topologie
        switch(gridData.topology) {
            case 'rectangular':
                return this.renderRectangularGrid(gridData);
            case 'hexagonal':
                return this.renderHexagonalGrid(gridData);
            case 'custom':
                return this.renderCustomGrid(gridData);
        }
    }
    
    renderRectangularGrid(gridData) {
        const subgrids = Object.values(gridData.subgrids);
        const gridLayout = this.calculateRectangularLayout(subgrids);
        
        subgrids.forEach((subgrid, index) => {
            const position = gridLayout.positions[index];
            this.renderSubGrid(subgrid, position);
        });
    }
    
    calculateRectangularLayout(subgrids) {
        // Automatische Berechnung optimaler Grid-Anordnung
        // Berücksichtigung von Bildschirmgröße und Subgrid-Anzahl
    }
}
```

### 4.2 Responsive Canvas-System

**Canvas Manager:**
```javascript
class CanvasManager {
    constructor(containerId) {
        this.container = document.getElementById(containerId);
        this.stage = new Konva.Stage({
            container: containerId,
            width: this.container.offsetWidth,
            height: this.container.offsetHeight
        });
        
        this.gameLayer = new Konva.Layer();
        this.effectsLayer = new Konva.Layer();
        this.uiLayer = new Konva.Layer();
        
        this.stage.add(this.gameLayer);
        this.stage.add(this.effectsLayer);
        this.stage.add(this.uiLayer);
        
        this.bindResizeHandler();
    }
    
    bindResizeHandler() {
        const resizeObserver = new ResizeObserver(entries => {
            for (let entry of entries) {
                this.handleResize(entry.contentRect);
            }
        });
        resizeObserver.observe(this.container);
    }
    
    handleResize(rect) {
        this.stage.width(rect.width);
        this.stage.height(rect.height);
        this.recalculateLayout();
    }
}
```

### 4.3 Visual Effects System

**Particle Effects für Scoring:**
```javascript
class ParticleEffectManager {
    constructor(layer) {
        this.layer = layer;
        this.activeEffects = [];
    }
    
    triggerScoringEffect(position, scoringMode, points) {
        const effectConfig = this.getEffectConfig(scoringMode);
        const particles = this.createParticles(position, effectConfig, points);
        
        particles.forEach(particle => {
            this.layer.add(particle);
            this.animateParticle(particle, effectConfig);
        });
    }
    
    getEffectConfig(scoringMode) {
        const configs = {
            'line_count': {
                color: '#4CAF50',
                particleCount: 10,
                spread: 50,
                duration: 1000
            },
            'territory': {
                color: '#2196F3',
                particleCount: 15,
                spread: 80,
                duration: 1500
            },
            'combo': {
                color: '#FF9800',
                particleCount: 20,
                spread: 100,
                duration: 2000
            }
        };
        return configs[scoringMode] || configs['line_count'];
    }
}
```

### 4.4 Interactive Cell System

**Cell Interaction Handling:**
```javascript
class InteractiveCell {
    constructor(x, y, size, cellData) {
        this.cellData = cellData;
        this.konvaGroup = new Konva.Group({
            x: x,
            y: y,
            width: size,
            height: size
        });
        
        this.background = new Konva.Rect({
            width: size,
            height: size,
            fill: this.getCellColor(),
            stroke: '#333',
            strokeWidth: 1
        });
        
        this.symbol = new Konva.Text({
            width: size,
            height: size,
            text: this.getCellSymbol(),
            fontSize: size * 0.6,
            fontFamily: 'Arial',
            fill: this.getSymbolColor(),
            align: 'center',
            verticalAlign: 'middle'
        });
        
        this.konvaGroup.add(this.background);
        this.konvaGroup.add(this.symbol);
        
        this.bindEvents();
    }
    
    bindEvents() {
        this.konvaGroup.on('click', () => {
            this.handleCellClick();
        });
        
        this.konvaGroup.on('mouseenter', () => {
            this.showHoverEffect();
        });
        
        this.konvaGroup.on('mouseleave', () => {
            this.hideHoverEffect();
        });
    }
    
    handleCellClick() {
        if (this.isClickable()) {
            GameStateManager.instance.makeMove(this.cellData.coordinates);
        }
    }
}
```

## 5. State Management und Datenfluss

### 5.1 Session Management

**Local Storage Integration:**
```javascript
class SessionManager {
    constructor() {
        this.storageKey = 'ttt_session';
        this.player = this.loadPlayer();
    }
    
    loadPlayer() {
        const stored = localStorage.getItem(this.storageKey);
        return stored ? JSON.parse(stored) : null;
    }
    
    savePlayer(playerData) {
        localStorage.setItem(this.storageKey, JSON.stringify(playerData));
        this.player = playerData;
    }
    
    clearSession() {
        localStorage.removeItem(this.storageKey);
        this.player = null;
    }
    
    getToken() {
        return this.player?.token || null;
    }
}
```

### 5.2 Game State Synchronization

**State Synchronization Manager:**
```javascript
class StateSyncManager {
    constructor(webSocketManager, gameStateManager) {
        this.ws = webSocketManager;
        this.gsm = gameStateManager;
        this.setupEventListeners();
    }
    
    setupEventListeners() {
        this.ws.addListener('update_game_state', (data) => {
            this.handleGameStateUpdate(data.game_state);
        });
        
        this.ws.addListener('player_disconnected', (data) => {
            this.handlePlayerDisconnect(data);
        });
        
        this.ws.addListener('game_paused', (data) => {
            this.showPauseUI(data);
        });
        
        this.ws.addListener('game_resumed', (data) => {
            this.hidePauseUI();
            this.handleGameStateUpdate(data.game_state);
        });
    }
    
    handleGameStateUpdate(gameState) {
        const transformedState = DataTransformer.transformGameState(gameState);
        this.gsm.updateState(transformedState);
        
        // Trigger visual updates
        this.updateGameBoard(transformedState);
        this.updateScoreDisplay(transformedState);
        this.triggerVisualEffects(transformedState);
    }
}
```

## 6. Responsive Design und Cross-Platform Considerations

### 6.1 CSS Grid System

**Responsive Layout:**
```css
/* Base Layout */
.screen {
    display: grid;
    grid-template-rows: auto 1fr auto;
    min-height: 100vh;
    padding: 1rem;
}

/* Game Board Responsive Layout */
.game-container {
    display: grid;
    grid-template-columns: 1fr auto;
    gap: 2rem;
    height: 100%;
}

/* Mobile Layout */
@media (max-width: 768px) {
    .game-container {
        grid-template-columns: 1fr;
        grid-template-rows: 1fr auto;
    }
    
    .game-sidebar {
        order: 2;
        max-height: 200px;
        overflow-y: auto;
    }
    
    #game-canvas-container {
        order: 1;
    }
}

/* Portrait Orientation */
@media (orientation: portrait) {
    .game-header {
        flex-direction: column;
        gap: 1rem;
    }
    
    .player-info {
        flex-wrap: wrap;
        justify-content: center;
    }
}
```

### 6.2 Canvas Responsive Scaling

**Adaptive Canvas Sizing:**
```javascript
class ResponsiveCanvasManager {
    constructor(canvasManager) {
        this.canvas = canvasManager;
        this.minCellSize = 30;
        this.maxCellSize = 80;
        this.aspectRatio = 16/9;
    }
    
    calculateOptimalLayout(gridData, containerSize) {
        const { width, height } = containerSize;
        const { subgridCount, cellsPerSubgrid } = this.analyzeGridStructure(gridData);
        
        // Berechne optimale Zellgröße basierend auf verfügbarem Platz
        const availableWidth = width * 0.9; // 10% padding
        const availableHeight = height * 0.9;
        
        const optimalCellSize = this.calculateCellSize(
            availableWidth, 
            availableHeight, 
            subgridCount, 
            cellsPerSubgrid
        );
        
        return {
            cellSize: Math.max(this.minCellSize, Math.min(this.maxCellSize, optimalCellSize)),
            gridLayout: this.calculateGridPositions(subgridCount, optimalCellSize)
        };
    }
}
```

## 7. Error Handling und User Experience

### 7.1 Connection Error Handling

**Robust Connection Management:**
```javascript
class ConnectionErrorHandler {
    constructor(webSocketManager, uiManager) {
        this.ws = webSocketManager;
        this.ui = uiManager;
        this.connectionState = 'connected';
        this.setupErrorHandling();
    }
    
    setupErrorHandling() {
        this.ws.addEventListener('disconnect', () => {
            this.handleDisconnection();
        });
        
        this.ws.addEventListener('reconnecting', (attempt) => {
            this.showReconnectionUI(attempt);
        });
        
        this.ws.addEventListener('reconnected', () => {
            this.handleReconnection();
        });
        
        this.ws.addEventListener('reconnect_failed', () => {
            this.showConnectionFailedUI();
        });
    }
    
    handleDisconnection() {
        this.connectionState = 'disconnected';
        this.ui.showConnectionOverlay();
        this.ui.disableGameInteractions();
    }
    
    showReconnectionUI(attempt) {
        this.ui.updateConnectionMessage(`Verbindungsversuch ${attempt}/5...`);
    }
    
    handleReconnection() {
        this.connectionState = 'connected';
        this.ui.hideConnectionOverlay();
        this.ui.enableGameInteractions();
        
        // Synchronize game state
        this.ws.sendMessage('reconnect', { 
            token: SessionManager.instance.getToken() 
        });
    }
}
```

### 7.2 User Feedback System

**Toast Notifications:**
```javascript
class NotificationManager {
    constructor() {
        this.container = this.createNotificationContainer();
        this.notifications = [];
    }
    
    show(message, type = 'info', duration = 3000) {
        const notification = this.createNotification(message, type);
        this.container.appendChild(notification);
        this.notifications.push(notification);
        
        // Auto-remove after duration
        setTimeout(() => {
            this.remove(notification);
        }, duration);
        
        // Animate in
        requestAnimationFrame(() => {
            notification.classList.add('show');
        });
    }
    
    createNotification(message, type) {
        const notification = document.createElement('div');
        notification.className = `notification notification--${type}`;
        notification.innerHTML = `
            <span class="notification__message">${message}</span>
            <button class="notification__close">&times;</button>
        `;
        
        notification.querySelector('.notification__close').addEventListener('click', () => {
            this.remove(notification);
        });
        
        return notification;
    }
}
```

## 8. Performance-Optimierung

### 8.1 Canvas Optimization

**Optimized Rendering:**
```javascript
class OptimizedRenderer {
    constructor() {
        this.dirtyRegions = [];
        this.animationFrame = null;
        this.isRendering = false;
    }
    
    markDirty(region) {
        this.dirtyRegions.push(region);
        this.scheduleRender();
    }
    
    scheduleRender() {
        if (!this.isRendering) {
            this.isRendering = true;
            this.animationFrame = requestAnimationFrame(() => {
                this.render();
                this.isRendering = false;
            });
        }
    }
    
    render() {
        // Only re-render dirty regions
        this.dirtyRegions.forEach(region => {
            this.renderRegion(region);
        });
        this.dirtyRegions = [];
    }
}
```

### 8.2 Memory Management

**Object Pooling für Particles:**
```javascript
class ParticlePool {
    constructor(initialSize = 50) {
        this.pool = [];
        this.active = [];
        
        // Pre-allocate particles
        for (let i = 0; i < initialSize; i++) {
            this.pool.push(this.createParticle());
        }
    }
    
    acquire() {
        return this.pool.pop() || this.createParticle();
    }
    
    release(particle) {
        particle.reset();
        this.pool.push(particle);
        
        const index = this.active.indexOf(particle);
        if (index > -1) {
            this.active.splice(index, 1);
        }
    }
}
```

## 9. Accessibility und Usability

### 9.1 Keyboard Navigation

**Comprehensive Keyboard Support:**
```javascript
class KeyboardNavigationManager {
    constructor(gameBoard) {
        this.gameBoard = gameBoard;
        this.focusedCell = null;
        this.bindKeyboardEvents();
    }
    
    bindKeyboardEvents() {
        document.addEventListener('keydown', (event) => {
            switch(event.code) {
                case 'ArrowUp':
                case 'ArrowDown':
                case 'ArrowLeft':
                case 'ArrowRight':
                    this.handleArrowKey(event.code);
                    event.preventDefault();
                    break;
                case 'Enter':
                case 'Space':
                    this.handleSelection();
                    event.preventDefault();
                    break;
                case 'Escape':
                    this.handleEscape();
                    break;
            }
        });
    }
    
    handleArrowKey(direction) {
        const newFocus = this.calculateNewFocus(this.focusedCell, direction);
        if (newFocus) {
            this.setFocus(newFocus);
        }
    }
}
```

### 9.2 Screen Reader Support

**ARIA Labels und Semantik:**
```html
<div class="game-cell" 
     role="button" 
     tabindex="0"
     aria-label="Zelle Reihe 2, Spalte 3, leer"
     aria-describedby="cell-help">
</div>

<div id="cell-help" class="sr-only">
    Drücken Sie Enter oder Leertaste um einen Zug zu machen
</div>

<div role="status" aria-live="polite" id="game-status">
    Alice ist am Zug
</div>

<div role="alert" aria-live="assertive" id="game-alerts">
    <!-- Wichtige Spielereignisse -->
</div>
```

## 10. Testing und Entwicklungsworkflow

### 10.1 Manual Testing Checklist

**Screen Testing:**
- [ ] Start Menu: Name input, token persistence, validation
- [ ] Lobby: Game list, refresh, create/join functionality
- [ ] Game Creation: Option loading, validation, preview
- [ ] Game Board: Rendering, interaction, responsive behavior
- [ ] Error States: Connection loss, invalid moves, timeouts

**Cross-Browser Testing:**
- [ ] Chrome/Chromium (latest)
- [ ] Firefox (latest)
- [ ] Safari (if available)
- [ ] Mobile browsers (responsive design)

### 10.2 Development Server Setup

**Simple HTTP Server für Testing:**
```javascript
// server.js (Node.js development server)
const express = require('express');
const path = require('path');

const app = express();
const PORT = 3000;

app.use(express.static(path.join(__dirname, 'frontend')));

app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'frontend', 'index.html'));
});

app.listen(PORT, () => {
    console.log(`Frontend server running on http://localhost:${PORT}`);
});
```

## 11. Integration Points mit Backend

### 11.1 WebSocket Message Handling

**Complete Message Handler Mapping:**
```javascript
class MessageHandlerRegistry {
    constructor() {
        this.handlers = new Map([
            // Authentication
            ['player_registered', this.handlePlayerRegistered],
            ['token_valid', this.handleTokenValid],
            ['token_invalid', this.handleTokenInvalid],
            
            // Lobby
            ['game_options_response', this.handleGameOptions],
            ['open_games_list', this.handleOpenGamesList],
            ['lobby_update', this.handleLobbyUpdate],
            
            // Game Management
            ['game_created', this.handleGameCreated],
            ['game_joined', this.handleGameJoined],
            ['player_ready', this.handlePlayerReady],
            ['game_started', this.handleGameStarted],
            
            // Gameplay
            ['update_game_state', this.handleGameStateUpdate],
            ['invalid_move', this.handleInvalidMove],
            
            // Connection Management
            ['player_disconnected', this.handlePlayerDisconnected],
            ['game_paused', this.handleGamePaused],
            ['player_reconnected', this.handlePlayerReconnected],
            ['game_resumed', this.handleGameResumed],
            ['game_aborted', this.handleGameAborted],
            ['reconnection_success', this.handleReconnectionSuccess],
            
            // Errors
            ['error', this.handleError]
        ]);
    }
}
```

### 11.2 Backend-Frontend Data Validation

**Client-Side Validation (Mirror von Backend):**
```javascript
class ClientSideValidator {
    static validateMove(position, gameState) {
        // Mirror backend validation logic
        const errors = [];
        
        if (!this.isPlayerTurn(gameState.currentPlayer)) {
            errors.push('Nicht dein Zug');
        }
        
        if (!this.isValidPosition(position, gameState.grid)) {
            errors.push('Ungültige Position');
        }
        
        if (!this.isCellEmpty(position, gameState.grid)) {
            errors.push('Zelle bereits belegt');
        }
        
        return errors;
    }
    
    static validateGameConfig(config) {
        // Validate configuration before sending to backend
        const errors = [];
        
        if (!config.field_variant) {
            errors.push('Spielfeld-Variante erforderlich');
        }
        
        // Add more validation rules...
        
        return errors;
    }
}
```

## 12. Deployment und Build Process

### 12.1 Build Pipeline (ohne komplexe Tools)

**Simple Build Script:**
```javascript
// build.js
const fs = require('fs');
const path = require('path');

class SimpleBuild {
    constructor() {
        this.srcDir = './src';
        this.distDir = './dist';
    }
    
    async build() {
        console.log('Starting build...');
        
        // Clean dist directory
        await this.clean();
        
        // Copy HTML files
        await this.copyHTML();
        
        // Bundle CSS
        await this.bundleCSS();
        
        // Bundle JavaScript
        await this.bundleJS();
        
        // Copy assets
        await this.copyAssets();
        
        console.log('Build complete!');
    }
    
    async bundleCSS() {
        // Concatenate CSS files
        const cssFiles = [
            'base.css',
            'components.css',
            'screens.css'
        ];
        
        let bundledCSS = '';
        for (const file of cssFiles) {
            const content = fs.readFileSync(path.join(this.srcDir, 'styles', file), 'utf8');
            bundledCSS += content + '\n';
        }
        
        fs.writeFileSync(path.join(this.distDir, 'bundle.css'), bundledCSS);
    }
}
```

## 13. Fazit und nächste Schritte

### 13.1 Implementierungs-Prioritäten

**Phase 1: Core Infrastructure**
1. Setup Basic HTML Structure
2. Implement WebSocket Manager
3. Create Session Manager
4. Build Start Menu Screen

**Phase 2: Game Infrastructure**
5. Implement Lobby Screen
6. Create Game Creation Screen
7. Setup Canvas Manager mit Konva.js
8. Build Basic Grid Renderer

**Phase 3: Game Logic Integration**
9. Implement Game Board Screen
10. Create Interactive Cell System
11. Add Visual Effects System
12. Implement State Synchronization

**Phase 4: Polish und UX**
13. Add Error Handling
14. Implement Responsive Design
15. Add Accessibility Features
16. Performance Optimization

### 13.2 Potentielle Backend-Inkonsistenzen

Während der Analyse sind mir folgende Punkte aufgefallen, die zwischen Backend-Plan und Frontend-Anforderungen abgestimmt werden sollten:

**1. Dynamische Topologie-Rendering:**
Der Backend-Plan beschreibt verschiedene Topologien (rectangular, hexagonal, custom), aber es fehlen Details über:
- Wie werden custom graph structures für das Frontend serialisiert?
- Welche Metadaten werden für hexagonal rendering benötigt?
- Wie werden neighbor relationships für rendering verwendet?

**2. Visual Effects Data:**
Für Partikel-Effekte benötigt das Frontend zusätzliche Informationen:
- Position der scoring events im Canvas-Koordinatensystem
- Stärke/Typ des Effects basierend auf scoring mode
- Timing-Informationen für synchronized effects

**3. Canvas Coordinate Mapping:**
- Backend arbeitet mit abstract coordinates (grid positions)
- Frontend benötigt pixel positions für rendering
- Mapping-Logic sollte im Backend oder Frontend implementiert werden?

**4. Real-time Performance:**
- Bei komplexen Topologien könnte complete game state transmission ineffizient werden
- Delta-Updates oder dirty region tracking könnte notwendig werden

**Empfehlung:** Diese Punkte sollten in einem gemeinsamen Backend-Frontend Meeting diskutiert und der Backend-Plan entsprechend erweitert werden.

### 13.3 Technische Schulden vermeiden

**Modulare Architektur:**
- Klare Trennung zwischen Game Logic (Backend) und Presentation (Frontend)
- Plugin-System auch im Frontend für verschiedene Renderer
- Configuration-driven UI generation

**Performance Monitoring:**
- Canvas rendering performance tracking
- WebSocket message frequency monitoring
- Memory usage monitoring bei particle effects

**Maintainability:**
- Ausführliche Code-Dokumentation
- Konsistente Naming Conventions
- Error Logging und Debugging Tools

Dieser detaillierte Frontend-Plan bietet eine solide Grundlage für die Implementierung des modularen Tic-Tac-Toe Frameworks unter Beibehaltung des KISS-Prinzips bei gleichzeitiger Berücksichtigung aller komplexen Anforderungen des Backend-Systems.