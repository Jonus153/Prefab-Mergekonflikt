# Merge-Konflikte in Unity und die Rolle von Prefabs

## 1. Wie entstehen Merge-Konflikte?

Git arbeitet textbasiert. Ein Merge-Konflikt entsteht, wenn:

1. Zwei Entwickler dieselbe Datei verändern.
2. Git die Änderungen nicht eindeutig automatisch zusammenführen kann.
3. Änderungen im selben oder überlappenden Bereich liegen.

Unity speichert Szenen (`.unity`) und Prefabs (`.prefab`) als YAML-Textdateien.  
Große Dateien mit vielen Objekten erhöhen die Wahrscheinlichkeit für Konflikte.

```mermaid
flowchart TD

A[Developer A ändert Datei]
B[Developer B ändert Datei]
C[Beide ändern dieselbe Datei]
D[Git versucht automatisch zu mergen]
E{Kann Git eindeutig kombinieren?}
F[Automatischer Merge]
G[Merge Konflikt entsteht]

A --> C
B --> C
C --> D
D --> E
E -- Ja --> F
E -- Nein --> G
```

---

## 2. Problem: Arbeiten ohne Prefabs

Wenn alle Objekte direkt in einer Szene gespeichert werden, arbeiten alle Entwickler an derselben Datei.

Beispiel:
- Entwickler A ändert den Player
- Entwickler B ändert den Enemy
- Beide verändern `MainScene.unity`

```mermaid
flowchart TD

Scene[MainScene.unity]

Player[Player Objekt]
Enemy[Enemy Objekt]
UI[UI Objekt]
Audio[AudioManager]

Scene --> Player
Scene --> Enemy
Scene --> UI
Scene --> Audio

DevA[Developer A ändert Player]
DevB[Developer B ändert Enemy]

DevA --> Scene
DevB --> Scene

Scene --> Conflict[Hohe Wahrscheinlichkeit für Merge Konflikt]
```

Obwohl unterschiedliche Objekte verändert werden, ist die betroffene Datei identisch.  
Dadurch steigt die Wahrscheinlichkeit für einen Merge-Konflikt erheblich.

---

## 3. Lösung: Modularisierung mit Prefabs

Prefabs teilen das Projekt in mehrere Dateien auf.

Statt alle Objekte in einer Szene zu speichern, werden sie als eigenständige `.prefab`-Dateien verwaltet.

```mermaid
flowchart TD

Scene[MainScene.unity]

PlayerPrefab[Player.prefab]
EnemyPrefab[Enemy.prefab]
UIPrefab[UI.prefab]

Scene --> PlayerPrefab
Scene --> EnemyPrefab
Scene --> UIPrefab

DevA[Developer A ändert Player.prefab]
DevB[Developer B ändert Enemy.prefab]

DevA --> PlayerPrefab
DevB --> EnemyPrefab

PlayerPrefab --> Safe[Kein Konflikt]
EnemyPrefab --> Safe
```

Jetzt arbeiten Entwickler an unterschiedlichen Dateien.  
Da Git dateibasiert arbeitet, entstehen keine Konflikte, solange verschiedene Dateien geändert werden.

---

## 4. Vergleich: Ohne vs. Mit Prefabs

```mermaid
flowchart LR

subgraph Ohne_Prefabs
    Scene1[MainScene.unity]
    Dev1A[Dev A]
    Dev1B[Dev B]

    Dev1A --> Scene1
    Dev1B --> Scene1
    Scene1 --> Conflict1[Merge Konflikt wahrscheinlich]
end

subgraph Mit_Prefabs
    Scene2[MainScene.unity]
    Player[Player.prefab]
    Enemy[Enemy.prefab]

    Dev2A --> Player
    Dev2B --> Enemy

    Scene2 --> Player
    Scene2 --> Enemy

    Player --> Safe2[Konflikt unwahrscheinlich]
    Enemy --> Safe2
end
```

---

## 5. Technischer Hintergrund

Unity speichert Objekte mit internen IDs, Referenzen und serialisierten Feldern im YAML-Format.  
Kleine Änderungen können große Textunterschiede erzeugen.

Je größer die Datei:
- desto mehr Objekte sind betroffen
- desto höher die Wahrscheinlichkeit für Überschneidungen
- desto schwieriger wird das automatische Zusammenführen

Durch die Aufteilung in mehrere Prefab-Dateien werden Änderungen isoliert gespeichert und Konflikte reduziert.

---

## Fazit

Merge-Konflikte in Unity entstehen hauptsächlich durch das gleichzeitige Bearbeiten großer Szenen-Dateien.

Prefabs sind relevant, weil sie:

- das Projekt modularisieren
- Änderungen auf mehrere Dateien verteilen
- paralleles Arbeiten ermöglichen
- die Konfliktwahrscheinlichkeit deutlich reduzieren

Eine saubere Prefab-Architektur ist daher essenziell für professionelle Teamarbeit mit Unity und Git.
