**Modulares Tic-Tac-Toe Game Framework**

Dieses Browser-basierte Spiele-Framework ermöglicht es, verschiedene erweiterte Tic-Tac-Toe-Varianten zu erstellen und zu spielen. Das System kombiniert klassische Teilfelder zu größeren Spielfeldern und bietet durch modulare Komponenten unendliche Variationsmöglichkeiten in Scoring, Aktivierung, Turn-Management und Win-Conditions.

1. **Framework-Konzept**

   * **Modulare Architektur**: Verschiedene Spielkomponenten (Scoring, Aktivierung, Turn-Management, Win-Conditions) sind austauschbar und kombinierbar.
   * **Graph-basierte Spielfelder**: Unterstützt verschiedene Topologien (rechteckig, hexagonal, custom) durch flexible Nachbarschafts-Definitionen.
   * **Plugin-System**: Neue Modi können ohne Kern-Änderungen über ein Protocol + Enum Hybrid-System hinzugefügt werden.
   * **Standard-Konfiguration**: Klassisches erweitertes Tic-Tac-Toe als Default-Setup für einfachen Einstieg.
   * **Type-Safe Erweiterbarkeit**: Kombiniert Enum-basierte Standard-Modi mit Protocol-basierten Custom-Erweiterungen.

2. **Standard-Spielkonfiguration (Default-Setup)**

   * **Spielfeld**: Konfigurierbare Anzahl von Teilfeldern (Standard: 3×3 Teilfelder, jedes mit 3×3 Zellen).
   * **Aktivierung**: Teilfelder werden standardmäßig in Spiralreihenfolge freigeschaltet (konfigurierbar).
   * **Turn-Management**: Spieler setzen entsprechend des gewählten Turn-Modus Züge (Standard: Alternating-Modus).
   * **Scoring**: Punkte werden nach konfigurierten Scoring-Modi vergeben (Standard: Line-Count für durchgehende Linien).
   * **Win-Condition**: Spielende und Gewinner werden nach gewählter Win-Condition bestimmt (Standard: Most-Points).

3. **Modulare Spielkomponenten**

   * **Activation-Modi**: 
     - Spiral (Standard), Player-Choice, Adjacent, Random, Capture, Time-Based
     - Bestimmen, welche Teilfelder wann aktiviert werden
   * **Scoring-Modi**: 
     - Line-Count (Standard), Territory, Combo, Pattern, Speed, Risk-Reward
     - Definieren, wie und wofür Punkte vergeben werden
   * **Turn-Management**: 
     - Alternating (Standard), Double-Turn, Auction, Time-Pressure, Simultaneous
     - Regeln, wer wann ziehen darf
   * **Win-Conditions**: 
     - Most-Points (Standard), First-To-X, Elimination, Territory-Control, Pattern-Complete
     - Bestimmen Spielende und Gewinner
   * **Field-Topologien**: 
     - Rectangular (Standard), Hexagonal, Torus, Custom-Graph-Strukturen
     - Verschiedene Spielfeld-Layouts und Nachbarschafts-Regeln
   * **Kombinierbarkeit**: Modi können je nach Kompatibilität kombiniert und gewichtet werden.

4. **Framework-Flexibilität & Beispiel-Konfigurationen**

   * **Standard-Spiel**: Spiral + Line-Count + Alternating + Most-Points
   * **Strategisches Spiel**: Player-Choice + Territory + Alternating + First-To-50
   * **Schnelles Spiel**: Random + Speed-Bonus + Time-Pressure + First-To-20
   * **Experimentelles Spiel**: Simultaneous + Pattern + Double-Turn + Pattern-Complete
   * **Custom-Kombinationen**: Benutzer können eigene Konfigurationen erstellen und teilen

5. **Benutzeroberfläche & Effekte**

   * Fünf Bildschirme: Startmenü, Lobby, Spiel-Erstellungsscreen, Spielbrett, Ergebnisanzeige.
   * **Startmenü**: Einmalige Eingabe des Spielernamens beim ersten Besuch. Nach der Eingabe wird ein Token generiert und lokal gespeichert.
   * **Session-Management**: 
     - Bei vorhandenem Token wird der Spieler direkt zur Lobby weitergeleitet.
     - Aus der Lobby kann man zurück zum Startmenü, um den Spielernamen zu ändern.
     - Token werden im localStorage des Browsers gespeichert.
     - **Reconnection-Handling**: Bei Verbindungsabbruch versucht das Frontend automatisch eine Wiederverbindung:
       - Nach erfolgreicher Reconnection wird der Spieler automatisch zum vorherigen Spiel-Zustand zurückgebracht
       - Keine Umleitung zur Lobby wenn ein aktives Spiel existiert
       - Token-basierte Authentifizierung gewährleistet nahtlose Wiederaufnahme
   * **Lobby**: Übersicht aller verfügbaren Spiele mit deren Status (wartend, laufend, voll). Möglichkeit zum Erstellen neuer Spiele oder Beitreten zu bestehenden.
   * **Spiel-Erstellungsscreen**: Konfiguration der Spielparameter (Field-Topologie, Activation-Mode, Scoring-Modi, Turn-Management, Win-Condition, Spielername, Symbol).
   * **Bereitschaftssystem**: Spieler können signalisieren, dass sie bereit sind zu starten. Das Spiel beginnt erst, wenn beide Spieler bereit sind.
   * **Connection-Loss-Handling**: 
     - Bei Verbindungsabbruch zeigt das Frontend automatisch eine Reconnection-Meldung
     - Verbliebene Spieler erhalten eine Pause-Benachrichtigung mit Abbruch-Option
     - **Pause-UI**: "Ein Spieler hat das Spiel verlassen. Warte bis er wieder eintritt oder beende das Spiel" 
     - Abbruch-Button ermöglicht sofortiges Spielende ohne Warten
     - Pause-Meldung verschwindet automatisch bei erfolgreicher Wiederverbindung des anderen Spielers
     - **Reconnection-Flow**: Bei Wiederverbindung wird Spieler direkt zum Spiel zurückgebracht (keine Lobby-Umleitung)
   * Visuelles Highlighting und ein auffälliger **Partikeleffekt** unterstreichen jede bewertete Aktion – Art und Stärke des Effekts abhängig vom aktiven Scoring-Modus.
   * Anzeige von aktuellem Spieler, Punktestand, aktiven Modi und Spielfortschritt.

6. **Technische Designanforderungen**

   * **KISS-Prinzip**: Die Anwendung bleibt einfach und übersichtlich, ohne unnötige Komplexität.
   * **Modulare Erweiterbarkeit**: Das System wird von Grund auf so konzipiert, dass es iterativ erweitert werden kann, ohne große Refactorings oder Tricks anwenden zu müssen. Dieses umfangreiche Dokument dient als Fundament für zukünftige Erweiterungen.
   * **Lokal**: Das Framework läuft in einem lokalen Netzwerk und benötigt keine Internetverbindung.
   * **Backend**:
     - Implementierung mit Python und Flask.
     - Die gesamte Framework-Logik, einschließlich der Validierung von Spielzügen, wird serverseitig abgewickelt.
     - Kommunikation mit dem Frontend erfolgt über Websockets.
     - **Mehrspiel-Verwaltung**: Server verwaltet mehrere Spiele über einfache Dictionaries und funktionale Ansätze.
     - **Session-Management**: Token-basierte Spieler-Authentifizierung über reine Funktionen.
     - **Connection-Tracking**: Überwachung von WebSocket-Verbindungen und automatische Disconnect-Erkennung.
     - **Pause/Resume-Logic**: Automatische Spielpause bei Disconnect, Wiederaufnahme bei Reconnection.
     - **Timeout-Management**: Automatischer Spiel-Abbruch nach konfigurierbarer Wartezeit bei längerer Inaktivität.
     - **Modulares Plugin-System**: Verschiedene Spielmodi werden über ein Protocol + Enum Hybrid-System implementiert.
     - **Raum-basierte Websockets**: Jedes Spiel läuft in einem eigenen Socket.IO-Raum.
     - Endpunkte für den Start des Spiels und Bereitschaftsstatus.
     - Validierung und Verarbeitung von Spielzügen über reine Funktionen.
   * **Frontend**:
     - HTML-Seiten für Startmenü, Lobby, Spiel-Erstellung und Spielbrett mit Canvas-Element.
     - JavaScript für die Websocket-Kommunikation, Session-Management (localStorage), Lobby-Verwaltung und Darstellung des Spielfelds.
     - **Connection-Management**: Automatische Reconnection-Versuche bei Verbindungsabbruch mit exponential backoff.
     - **Pause-UI**: Dedicated UI-Komponenten für Disconnect-Benachrichtigungen und Spiel-Abbruch-Optionen.
     - **State-Persistence**: Erhaltung des Spiel-Zustands während Reconnection ohne Lobby-Umleitung.
     - CSS für die Anpassung des Layouts und die Skalierung des Canvas.
     - **Canvas-Rendering**: Adaptives Rendering-System für verschiedene Spielfeld-Topologien und Graph-Strukturen.
     - **Responsive Design**: Automatische Anpassung an verschiedene Bildschirmgrößen und Spielfeld-Formen.
     - Das Frontend dient ausschließlich der visuellen Darstellung und Interaktion.
   * **Canvas-Flexibilität**:
     - Das Canvas-System passt sich dynamisch an verschiedene Topologien an (rechteckig, hexagonal, custom).
     - Elemente skalieren proportional basierend auf der gewählten Spielfeld-Struktur.

7. **Kommunikation zwischen Client und Server**

   * **Websockets**:
     - Der Server sendet nach jedem Zug den aktualisierten Spielstatus an alle Teilnehmer des jeweiligen Spiels.
     - Der Client sendet Spielzüge, Lobby-Aktionen und Bereitschaftsstatus an den Server.
     - **Raum-System**: Jedes Spiel läuft in einem eigenen Socket.IO-Raum für isolierte Kommunikation.
   * **Nachrichtenformat**:
     - JSON wird für die Kommunikation verwendet.
     - Beispiel-Nachrichten vom Client: 
       - `{ "action": "register_player", "player_name": "Alice" }`
       - `{ "action": "validate_token", "token": "abc123xyz" }`
       - `{ "action": "update_player_name", "token": "abc123xyz", "new_name": "Alice2" }`
       - `{ "action": "get_game_options" }`
       - `{ "action": "list_open_games" }`
       - `{ "action": "create_game", "token": "abc123xyz", "game_config": {"field_variant": "rectangular_3x3", "activation_mode": "spiral", "scoring_modes": ["line_count"], "turn_mode": "alternating", "win_condition": "most_points", "special_rules": []}, "player_info": {"name": "Alice", "symbol": "X"} }`
       - `{ "action": "join_game", "token": "abc123xyz", "game_id": "abc123", "player_info": {"name": "Bob", "symbol": "O"} }`
       - `{ "action": "ready", "token": "abc123xyz", "game_id": "abc123" }`
       - `{ "action": "make_move", "token": "abc123xyz", "game_id": "abc123", "position": [1, 2] }`
       - `{ "action": "reconnect", "token": "abc123xyz" }`
       - `{ "action": "abort_game", "token": "abc123xyz", "game_id": "abc123" }`
     - Beispiel-Nachrichten vom Server:
       - `{ "type": "player_registered", "token": "abc123xyz", "player": {"name": "Alice"} }`
       - `{ "type": "token_valid", "player": {"name": "Alice"} }`
       - `{ "type": "token_invalid" }`
       - `{ "type": "game_options_response", "options": {"field_variants": ["rectangular_3x3", "rectangular_4x4", "rectangular_5x5"], "activation_modes": ["spiral", "player_choice", "adjacent", "random"], "scoring_modes": ["line_count", "territory", "combo", "pattern"], "turn_modes": ["alternating", "double_turn", "time_pressure"], "win_conditions": ["most_points", "first_to_x", "territory_control"], "special_rules": ["capture", "time_based", "risk_reward"]} }`
       - `{ "type": "open_games_list", "games": [{"game_id": "abc123", "creator": "Alice", "status": "waiting", "player_count": 1, "max_players": 2, "config": {...}}] }`
       - `{ "type": "lobby_update", "games": [...] }`
       - `{ "type": "game_created", "game_id": "abc123", "creator": "Alice" }`
       - `{ "type": "game_joined", "game_id": "abc123", "players": [...] }`
       - `{ "type": "player_ready", "player": "Alice", "all_ready": false }`
       - `{ "type": "game_started", "game_state": {...} }`
       - `{ "type": "update_game_state", "game_state": {"global_grid": {...}, "current_player": "Alice", "scores": {"Alice": 3, "Bob": 2}, "active_modes": {"scoring": ["line_count"], "activation": "spiral"}, "game_phase": "playing", "is_finished": false, "last_move": {"player": "Bob", "position": [1, 2], "timestamp": "2023-..."}} }`
       - `{ "type": "invalid_move", "reason": "cell_already_occupied", "details": "The selected cell at position [1, 2] is already occupied by player Alice" }`
       - `{ "type": "player_disconnected", "player": "Bob", "game_id": "abc123", "message": "Ein Spieler hat das Spiel verlassen. Warte bis er wieder eintritt oder beende das Spiel." }`
       - `{ "type": "game_paused", "game_id": "abc123", "disconnected_player": "Bob", "can_abort": true }`
       - `{ "type": "player_reconnected", "player": "Bob", "game_id": "abc123" }`
       - `{ "type": "game_resumed", "game_id": "abc123", "game_state": {...} }`
       - `{ "type": "game_aborted", "game_id": "abc123", "aborted_by": "Alice", "reason": "player_disconnect" }`
       - `{ "type": "reconnection_success", "game_id": "abc123", "game_state": {...} }`
       - `{ "type": "error", "message": "Invalid action or game state" }`

   * **Spielzug-Validierung und Zustandsaktualierung**:
     - **Move-Validierung**: Wenn das Backend eine `make_move`-Nachricht erhält, validiert es:
       - Ist der Spieler an der Reihe (gemäß aktuellem `turn_mode`)?
       - Ist die angegebene Position gültig und innerhalb des aktiven Teilfelds?
       - Ist die Zelle frei (nicht bereits von einem anderen Spieler belegt)?
       - Entspricht der Zug den aktuellen Spielregeln und `special_rules`?
     - **Gültiger Zug**: Nach erfolgreicher Validierung wird der neue `GameState` berechnet:
       - Die Zelle wird mit dem Spielersymbol markiert (serverseitige Logik bestimmt Cell-Zustand)
       - **Cell-State-Berechnung**: Das Backend aktualisiert `Cell.player` und `Cell.properties`
         - Grundzustand: `Cell.player` wird auf den aktuellen Spieler gesetzt
         - Special-Rules können zusätzliche Properties setzen (z.B. für Capture-Modi)
         - Nachbarschafts-Updates für graph-basierte Spielfelder werden angewendet
       - Scoring-Modi bewerten den Zug und aktualisieren Punktestände
       - Activation-Modi bestimmen das nächste aktive Teilfeld
       - Turn-Management bestimmt den nächsten Spieler
       - Win-Conditions prüfen auf Spielende
     - **State-Update**: Eine `update_game_state`-Nachricht wird an alle Spieler des Spiels gesendet mit:
       - Vollständigem neuen `GameState` (Grid, Scores, aktiver Spieler, etc.)
       - Details zum letzten Zug (Spieler, Position, Zeitstempel)
       - Informationen über Scoring-Events und Punkte-Änderungen
     - **Ungültiger Zug**: Bei Validierungsfehlern wird eine `invalid_move`-Nachricht nur an den sendenden Spieler geschickt mit:
       - Spezifischem Fehlergrund (`reason`: "not_your_turn", "cell_occupied", "invalid_position", "game_not_active")
       - Detaillierter Beschreibung des Fehlers (`details`)

   * **Verbindungsabbruch und Spielpause**:
     - **Disconnect-Erkennung**: Der Server erkennt Verbindungsabbrüche über WebSocket-Disconnect-Events
     - **Automatische Spielpause**: Bei Disconnect eines Spielers während eines laufenden Spiels:
       - Spielstatus wird auf "paused" gesetzt
       - Verbliebene Spieler erhalten `player_disconnected` und `game_paused` Nachrichten
       - Pause-Nachricht enthält Option zum Spiel-Abbruch (`can_abort: true`)
       - Spielzüge werden während der Pause nicht akzeptiert
     - **Reconnection-Handling**: Bei Wiederverbindung eines disconnected Spielers:
       - Server validiert Token und stellt Spielzustand wieder her
       - `reconnect`-Action führt zu automatischer Spiel-Wiederaufnahme
       - Alle Spieler erhalten `player_reconnected` und `game_resumed` Nachrichten
       - Spielstatus wechselt zurück zu "running"
     - **Spiel-Abbruch**: Verbliebene Spieler können wartende Spiele über `abort_game` beenden:
       - Spiel wird als "aborted" markiert und beendet
       - Alle Clients erhalten `game_aborted` Nachricht
       - Spieler werden zur Lobby zurückgeleitet
     - **Zeitbasierte Timeouts**: Nach konfigurierter Zeit (z.B. 5 Minuten) wird pausiertes Spiel automatisch abgebrochen

   * **Spieler- und Sitzungsverwaltung**:
     - **Spiel-Erstellung**: Die `create_game`-Nachricht enthält sowohl `game_config` als auch `player_info`
       - Der Ersteller wird automatisch als erster Spieler dem Spiel hinzugefügt
       - Das Spiel erhält den Status "waiting" und wartet auf weitere Spieler
     - **Spiel-Beitritt**: Spieler können über `join_game` bestehenden Spielen beitreten
       - Validation erfolgt auf verfügbare Plätze und Spielstatus
       - Alle Spieler des Spiels erhalten eine `game_joined`-Nachricht mit aktualisierter Spielerliste
     - **Lobby-Management**: 
       - `list_open_games` gibt alle Spiele im Status "waiting" zurück
       - `lobby_update`-Nachrichten informieren über Änderungen in verfügbaren Spielen
       - Spiele werden automatisch aus der Lobby entfernt wenn sie voll oder gestartet sind
     - **Automatische Updates**: Jede Änderung an Spielern (Beitritt, Bereitschaft, Verlassen) triggert Updates an alle betroffenen Clients
     - **Disconnect-Management**: 
       - Server verfolgt Connection-Status aller Spieler in aktiven Spielen
       - Automatische Pause bei Disconnect, Wiederaufnahme bei Reconnect
       - Spieler können via Token nahtlos zu unterbrochenen Spielen zurückkehren
       - Timeout-basierter Spiel-Abbruch bei längerer Inaktivität

8. **Modulare Architektur & Erweiterbarkeit**

   * **Grundprinzip**: Das System wird als flexibles, graph-basiertes Framework konzipiert, das verschiedene Spielvarianten ohne Kern-Refactoring ermöglicht.

   * **Dynamische Spielkonfiguration**: 
     - Das Frontend fragt über `get_game_options` die verfügbaren Module vom Backend ab
     - Das Backend antwortet mit `game_options_response` und Listen aller verfügbaren Optionen
     - Dies ermöglicht wahre Modularität: Neue Module werden automatisch im Frontend verfügbar
     - Keine Hard-Codierung von Optionen im Frontend - alles wird dynamisch geladen
     - Kompatibilitätsvalidierung erfolgt serverseitig basierend auf verfügbaren Modulen

   * **Graph-basierte Spielfeld-Modellierung**:
     - **Cell-Dataclass**: `id` (str), `player` (Optional[Player]), `coordinates` (Tuple), `neighbors` (List[str]), `properties` (Dict)
     - **SubGrid-Dataclass**: `id` (str), `cells` (Dict[str, Cell]), `shape_definition` (Dict), `is_closed` (bool), `properties` (Dict)
     - **GlobalGrid-Dataclass**: `subgrids` (Dict[str, SubGrid]), `subgrid_connections` (Dict[str, List[str]]), `topology` (str)

   * **Event-basiertes Scoring-System**:
     - **GameEvent-Dataclass**: `type` (str), `player` (Player), `data` (Dict), `timestamp` (datetime)
     - **ScoreComponent-Dataclass**: `name` (str), `weight` (float), `active` (bool)
     - Verschiedene Scoring-Modi können parallel und gewichtet laufen

   * **State Machine für Turn-Management**:
     - **GameState-Dataclass**: `current_phase` (str), `active_players` (List[Player]), `phase_data` (Dict), `available_actions` (List[str])
     - Ermöglicht verschiedene Turn-Modi (alternierend, simultan, Auktion, etc.)

   * **Protocol + Enum Hybrid für Type-Safety**:
     - **Standard-Modi**: Als Enums definiert für IDE-Unterstützung und Type-Checking
     - **Custom-Modi**: Über Protocol-basierte Registries erweiterbar
     - **Beispiel-Protocols**: `ScoringStrategy`, `ActivationStrategy`, `WinConditionChecker`

   * **Plugin-System Beispiele**:
     ```python
     # Standard-Modi (Type-safe)
     class StandardScoringMode(Enum):
         LINE_COUNT = "line_count"
         TERRITORY = "territory"
         COMBO = "combo"
     
     # Protocol für Erweiterungen
     class ScoringStrategy(Protocol):
         def calculate_score(self, event: GameEvent, context: GameState) -> Dict[Player, int]: ...
         def get_name(self) -> str: ...
     
     # Registry kombiniert beide
     class ScoringRegistry:
         @classmethod
         def get(cls, mode: Union[StandardScoringMode, str]) -> ScoringStrategy: ...
     ```

   * **Modulare Komponenten**:
     - **Activation-Strategien**: Spiral, Player-Choice, Adjacent, Random, Capture, Time-Based
     - **Scoring-Modi**: Line-Count, Territory, Combo, Pattern, Speed, Risk-Reward  
     - **Turn-Management**: Alternating, Double-Turn, Auction, Time-Pressure, Simultaneous
     - **Win-Conditions**: Most-Points, First-To-X, Elimination, Territory-Control, Pattern-Complete
     - **Field-Variants**: Different sizes, topologies (including future hexagonal, torus, etc.)

9. **Datenmodell-Details**

   * **Backend**:
     - Flask-App mit Websocket-Unterstützung (z. B. über Flask-SocketIO).
     - **Mehrspiel-Verwaltung**: Server verwaltet mehrere Spiele über einfache Dictionaries und funktionale Ansätze.
     - **Session-Management**: Token-basierte Spieler-Authentifizierung über reine Funktionen.
     - **Connection-Management**: Tracking von WebSocket-Verbindungen pro Spieler und Spiel.
     - **Lobby-System**: Endpunkte nutzen funktionale Module für das Erstellen, Auflisten und Beitreten zu Spielen.
     - **Raum-basierte Websockets**: Jedes Spiel läuft in einem eigenen Socket.IO-Raum.
     - Endpunkte für den Start des Spiels und Bereitschaftsstatus.
     - Validierung und Verarbeitung von Spielzügen über reine Funktionen.
   * **Frontend**:
     - HTML-Seiten für Startmenü, Lobby, Spiel-Erstellung und Spielbrett mit Canvas-Element.
     - JavaScript für die Websocket-Kommunikation, Session-Management (localStorage), Lobby-Verwaltung und Darstellung des Spielfelds.
     - **Reconnection-Logic**: Automatische Wiederverbindung mit Token-basierter Session-Wiederherstellung.
     - **Pause-UI-Management**: Handling von Disconnect-Benachrichtigungen und Abbruch-Optionen.
     - CSS für die Anpassung des Layouts und die Skalierung des Canvas.
   * **Kern-Dataclasses**:
     - Verwendung von Python-Dataclasses (@dataclass) für saubere Objektdefinitionen ohne Methoden.
     - **Player-Dataclass**: Speichert `name` (str), `symbol` (str), `token` (str), `is_ready` (bool) und `is_connected` (bool).
     - **Cell-Dataclass**: Speichert `id` (str), `player` (Optional[Player]), `coordinates` (Tuple), `neighbors` (List[str]), `properties` (Dict).
     - **SubGrid-Dataclass**: Enthält `id` (str), `cells` (Dict[str, Cell]), `shape_definition` (Dict), `is_closed` (bool), `properties` (Dict).
     - **GlobalGrid-Dataclass**: Enthält `subgrids` (Dict[str, SubGrid]), `subgrid_connections` (Dict[str, List[str]]), `topology` (str), `active_subgrid_pos` (Optional[str]).
     - **GameEvent-Dataclass**: `type` (str), `player` (Player), `data` (Dict[str, Any]), `timestamp` (datetime).
     - **GameState-Dataclass**: Zentrale Spielverwaltung mit `global_grid` (GlobalGrid), `current_phase` (str), `active_players` (List[Player]), `phase_data` (Dict), `available_actions` (List[str]), `scores` (Dict[Player, int]), `is_finished` (bool), `disconnected_players` (List[Player]) und `pause_timestamp` (Optional[datetime]).
     - **GameConfig-Dataclass**: `field_variant` (str), `activation_mode` (str), `scoring_modes` (List[str]), `turn_mode` (str), `win_condition` (str), `special_rules` (List[str]).
     - **Game-Dataclass**: Verwaltet ein einzelnes Spiel mit `game_id` (str), `config` (GameConfig), `game_state` (GameState), `status` (str: "waiting", "ready", "running", "paused", "finished", "aborted"), `created_at` (datetime) und `last_activity` (datetime).
     - **Funktionale Architektur**: 
       - `player_functions.py`: Funktionen für Spieler-Registrierung, Token-Validierung, Namensänderung, Connection-Status-Management.
       - `game_functions.py`: Funktionen für Spiel-Erstellung, Beitreten, Züge, Event-Verarbeitung, Pause/Resume-Logic.
       - `lobby_functions.py`: Funktionen für Lobby-Verwaltung, Spiel-Auflistung, Disconnect-Cleanup.
       - `scoring_modules/`: Verschiedene Scoring-Strategien als Protocol-implementierende Module.
       - `activation_modules/`: Verschiedene Aktivierungs-Strategien für Teilfelder.
       - `turn_modules/`: Verschiedene Turn-Management-Strategien.
       - `win_condition_modules/`: Verschiedene Gewinn-Bedingungen.
       - Alle Funktionen arbeiten mit den Dataclasses und geben neue Instanzen zurück (immutable Ansatz).

10. **Nächste Schritte**

   * Einrichtung eines Flask-Backends mit Websocket-Unterstützung und funktionaler Mehrspiel-Verwaltung.
   * Definition aller Kern-Dataclasses ohne Methoden für einfache Serialisierung.
   * Implementierung der Protocol-basierten Plugin-Architektur mit Standard-Enum-Unterstützung.
   * Entwicklung der graph-basierten Spielfeld-Struktur für maximale Flexibilität.
   * Implementierung des Event-basierten Scoring-Systems.
   * Entwicklung der State Machine für flexibles Turn-Management.
   * Implementierung der funktionalen Module (player_functions, game_functions, lobby_functions).
   * Entwicklung der Standard-Modi-Module (scoring, activation, turn, win_condition).
   * Entwicklung des Session-Managements über reine Funktionen mit Token-basierter Authentifizierung und Reconnection-Handling.
   * Implementierung des Disconnect/Reconnect-Systems mit automatischer Spielpause und Wiederaufnahme.
   * Implementierung des Lobby-Systems und der Spiel-Erstellung mit modularer Konfiguration.
   * Entwicklung des Bereitschaftssystems für Spieler.
   * Erstellung der Frontend-Screens mit localStorage-Integration für Token-Verwaltung.
   * Implementierung der raum-basierten Websocket-Kommunikation mit Connection-Tracking.
   * Entwicklung der Disconnect/Reconnect-Behandlung mit automatischer Pause-Logic und Timeout-Management.
   * Implementierung der Frontend-Reconnection-Logic mit State-Persistence und Pause-UI.
   * Entwicklung der Canvas-basierten Spielfeld-Darstellung mit dynamischer Skalierung für verschiedene Topologien und Graph-Strukturen.
   * Implementierung der serverseitigen Logik für die Validierung und Abwicklung von Spielzügen über reine Funktionen.
   * Entwicklung eines Konfigurations-Validators für Spielmodus-Kompatibilität.

Kurz gesagt kombiniert das Framework den strategischen Kern von Tic-Tac-Toe mit modularen Spielkomponenten, graph-basierten Spielfeldern und einem flexiblen Plugin-System, wodurch unendlich viele Spielvarianten ermöglicht werden – von einfachen erweiterten Tic-Tac-Toe-Spielen bis hin zu komplexen strategischen Brettspielen.
