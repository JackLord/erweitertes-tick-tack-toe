**Mo1. **Framework-Konzept**

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
   * **Kombinierbarkeit**: Modi können je nach Kompatibilität kombiniert und gewichtet werden.oe Game Framework**

Dieses Browser-basierte Spiele-Framework ermöglicht es, verschiedene erweiterte Tic-Tac-Toe-Varianten zu erstellen und zu spielen. Das System kombiniert klassische Teilfelder zu größeren Spielfeldern und bietet durch modulare Komponenten unendliche Variationsmöglichkeiten in Scoring, Aktivierung, Turn-Management und Win-Conditions.

1. **Aufbau**

   * Beim Start wählt man die Anzahl der Teilfelder pro Seite (1 × 1 bis 5 × 5).
   * Jedes Teilfeld ist selbst ein normales 3 × 3-Tic-Tac-Toe-Brett.
   * Alle Teilfelder zusammen ergeben ein „globales“ Raster von *(Teilfelder × 3)* Zellen pro Seite.

2. **Spielablauf**

   * Zwei Spieler setzen abwechselnd in das gerade **aktive** Teilfeld.
   * Die Teilfelder werden nacheinander in **Spiralreihenfolge** freigeschaltet; alle anderen Felder sind solange gesperrt.
   * Ein Teilfeld schließt sich, sobald alle neun Zellen belegt sind, und das nächste Teilfeld wird aktiv.

3. **Punktsystem**

   * Nach jedem Zug prüft das Spiel das **gesamte** globale Raster.
   * Für jede neu entstandene durchgehende Linie von mindestens drei gleichen Symbolen (horizontal, vertikal oder diagonal) erhält der aktuelle Spieler **einen Punkt**.
   * Mehrere Linien in einem Zug bringen entsprechend mehrere Punkte.

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
   * **Lobby**: Übersicht aller verfügbaren Spiele mit deren Status (wartend, laufend, voll). Möglichkeit zum Erstellen neuer Spiele oder Beitreten zu bestehenden.
   * **Spiel-Erstellungsscreen**: Konfiguration der Spielparameter (Field-Topologie, Activation-Mode, Scoring-Modi, Turn-Management, Win-Condition, Spielername, Symbol).
   * **Bereitschaftssystem**: Spieler können signalisieren, dass sie bereit sind zu starten. Das Spiel beginnt erst, wenn beide Spieler bereit sind.
   * Visuelles Highlighting und ein auffälliger **Partikeleffekt** unterstreichen jede bewertete Aktion – Art und Stärke des Effekts abhängig vom aktiven Scoring-Modus.
   * Anzeige von aktuellem Spieler, Punktestand, aktiven Modi und Spielfortschritt.

6. **Technische Designanforderungen**

   * **KISS-Prinzip**: Die Anwendung bleibt einfach und übersichtlich, ohne unnötige Komplexität.
   * **Modulare Erweiterbarkeit**: Das System wird von Grund auf so konzipiert, dass es iterativ erweitert werden kann, ohne große Refactorings oder Tricks anwenden zu müssen. Dieses umfangreiche Dokument dient als Fundament für zukünftige Erweiterungen.
   * **Lokal**: Das Spiel läuft in einem lokalen Netzwerk und benötigt keine Internetverbindung.
   * **Backend**:
     - Implementierung mit Python und Flask.
     - Die gesamte Spiel-Logik, einschließlich der Validierung von Spielzügen, wird serverseitig abgewickelt.
     - Kommunikation mit dem Frontend erfolgt über Websockets.
     - **Mehrspiel-Verwaltung**: Server verwaltet mehrere Spiele über einfache Dictionaries und funktionale Ansätze.
     - **Session-Management**: Token-basierte Spieler-Authentifizierung über reine Funktionen.
     - **Modulares Plugin-System**: Verschiedene Spielmodi werden über ein Protocol + Enum Hybrid-System implementiert.
     - **Raum-basierte Websockets**: Jedes Spiel läuft in einem eigenen Socket.IO-Raum.
     - Endpunkte für den Start des Spiels und Bereitschaftsstatus.
     - Validierung und Verarbeitung von Spielzügen über reine Funktionen.
   * **Frontend**:
     - HTML-Seiten für Startmenü, Lobby, Spiel-Erstellung und Spielbrett mit Canvas-Element.
     - JavaScript für die Websocket-Kommunikation, Session-Management (localStorage), Lobby-Verwaltung und Darstellung des Spielfelds.
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
       - `{ "action": "create_game", "token": "abc123xyz", "game_config": {"field_variant": "rectangular_3x3", "activation_mode": "spiral", "scoring_modes": ["line_count"], "turn_mode": "alternating", "win_condition": "most_points", "symbol": "X"} }`
       - `{ "action": "join_game", "token": "abc123xyz", "game_id": "abc123", "symbol": "O" }`
       - `{ "action": "ready", "token": "abc123xyz", "game_id": "abc123" }`
       - `{ "action": "move", "token": "abc123xyz", "game_id": "abc123", "position": [1, 2] }`
     - Beispiel-Nachrichten vom Server:
       - `{ "type": "player_registered", "token": "abc123xyz", "player": {"name": "Alice"} }`
       - `{ "type": "token_valid", "player": {"name": "Alice"} }`
       - `{ "type": "token_invalid" }`
       - `{ "type": "lobby_update", "games": [...] }`
       - `{ "type": "game_joined", "game_id": "abc123", "players": [...] }`
       - `{ "type": "player_ready", "player": "Alice", "all_ready": false }`
       - `{ "type": "game_started", "game_state": {...} }`
       - `{ "type": "game_update", "grid": [...], "scores": {"Alice": 3, "Bob": 2}, "active_modes": {"scoring": ["line_count"], "activation": "spiral"}, "game_phase": "playing" }`

8. **Modulare Architektur & Erweiterbarkeit**

   * **Grundprinzip**: Das System wird als flexibles, graph-basiertes Framework konzipiert, das verschiedene Spielvarianten ohne Kern-Refactoring ermöglicht.

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

9. **Datenmodell-Details**

   * **Backend**:
     - Flask-App mit Websocket-Unterstützung (z. B. über Flask-SocketIO).
     - **Mehrspiel-Verwaltung**: Server verwaltet mehrere Spiele über einfache Dictionaries und funktionale Ansätze.
     - **Session-Management**: Token-basierte Spieler-Authentifizierung über reine Funktionen.
     - **Lobby-System**: Endpunkte nutzen funktionale Module für das Erstellen, Auflisten und Beitreten zu Spielen.
     - **Raum-basierte Websockets**: Jedes Spiel läuft in einem eigenen Socket.IO-Raum.
     - Endpunkte für den Start des Spiels und Bereitschaftsstatus.
     - Validierung und Verarbeitung von Spielzügen über reine Funktionen.
   * **Frontend**:
     - HTML-Seiten für Startmenü, Lobby, Spiel-Erstellung und Spielbrett mit Canvas-Element.
     - JavaScript für die Websocket-Kommunikation, Session-Management (localStorage), Lobby-Verwaltung und Darstellung des Spielfelds.
     - CSS für die Anpassung des Layouts und die Skalierung des Canvas.
   * **Kern-Dataclasses**:
     - Verwendung von Python-Dataclasses (@dataclass) für saubere Objektdefinitionen ohne Methoden.
     - **Player-Dataclass**: Speichert `name` (str), `symbol` (str), `token` (str) und `is_ready` (bool).
     - **Cell-Dataclass**: Speichert `id` (str), `player` (Optional[Player]), `coordinates` (Tuple), `neighbors` (List[str]), `properties` (Dict).
     - **SubGrid-Dataclass**: Enthält `id` (str), `cells` (Dict[str, Cell]), `shape_definition` (Dict), `is_closed` (bool), `properties` (Dict).
     - **GlobalGrid-Dataclass**: Enthält `subgrids` (Dict[str, SubGrid]), `subgrid_connections` (Dict[str, List[str]]), `topology` (str), `active_subgrid_pos` (Optional[str]).
     - **GameEvent-Dataclass**: `type` (str), `player` (Player), `data` (Dict[str, Any]), `timestamp` (datetime).
     - **GameState-Dataclass**: Zentrale Spielverwaltung mit `global_grid` (GlobalGrid), `current_phase` (str), `active_players` (List[Player]), `phase_data` (Dict), `available_actions` (List[str]), `scores` (Dict[Player, int]), `is_finished` (bool).
     - **GameConfig-Dataclass**: `field_variant` (str), `activation_mode` (str), `scoring_modes` (List[str]), `turn_mode` (str), `win_condition` (str), `special_rules` (List[str]).
     - **Game-Dataclass**: Verwaltet ein einzelnes Spiel mit `game_id` (str), `config` (GameConfig), `game_state` (GameState), `status` (str: "waiting", "ready", "running", "finished"), `created_at` (datetime).
     - **Funktionale Architektur**: 
       - `player_functions.py`: Funktionen für Spieler-Registrierung, Token-Validierung, Namensänderung.
       - `game_functions.py`: Funktionen für Spiel-Erstellung, Beitreten, Züge, Event-Verarbeitung.
       - `lobby_functions.py`: Funktionen für Lobby-Verwaltung, Spiel-Auflistung.
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
   * Entwicklung des Session-Managements über reine Funktionen mit Token-basierter Authentifizierung.
   * Implementierung des Lobby-Systems und der Spiel-Erstellung mit modularer Konfiguration.
   * Entwicklung des Bereitschaftssystems für Spieler.
   * Erstellung der Frontend-Screens mit localStorage-Integration für Token-Verwaltung.
   * Implementierung der raum-basierten Websocket-Kommunikation.
   * Entwicklung der Canvas-basierten Spielfeld-Darstellung mit dynamischer Skalierung für verschiedene Topologien und Graph-Strukturen.
   * Implementierung der serverseitigen Logik für die Validierung und Abwicklung von Spielzügen über reine Funktionen.
   * Entwicklung eines Konfigurations-Validators für Spielmodus-Kompatibilität.

Kurz gesagt kombiniert das Framework den strategischen Kern von Tic-Tac-Toe mit modularen Spielkomponenten, graph-basierten Spielfeldern und einem flexiblen Plugin-System, wodurch unendlich viele Spielvarianten ermöglicht werden – von einfachen erweiterten Tic-Tac-Toe-Spielen bis hin zu komplexen strategischen Brettspielen.
