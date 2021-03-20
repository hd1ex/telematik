# Router

Ein Router leitet Daten von Eingabeport(s) an Ausgabeport(s) weiter.

Das Weiterleiten von Daten ist eine Aufgabe des **Datenpfads**.

## Geräte für Layer
- Hubs -> Layer 1
- Bridges -> Layer 2
- Routers -> Layer 3

## Routing
- Aufgabe des Kontrollpfades

### Router
- Leitet Daten an Port weiter
- Weiterleiten mit der spezifizierten Geschwindigkeit des Ausgangs (line speed)
- Kurze Warteschlangen
- Möglichst kleine Weiterleitungstabellen (kleiner Speicher)

### Aufgaben
- IP-Version prüfen
- Checksumme prüfen
- Time to live dekrementieren
- Checksumme anpassen

### Typen
Core router
- von service providern
- große Datenraten
- schnelle Hardware erforderlich
- Redundanz
- Kosten sind zweitrangig

Enterprise router
- verschiedene Systeme in Unternehmen und Hochschulen verbinden
- Konnektivität für große Anzahl an Endsystemen schaffen
- VLANs, firewalls
- kleine Kosten pro Port
- große Anzahl von Ports
- Leichte Wartbarkeit

Edge router (access router)
- am äußeren Ende zum service provider
- schaffen Konnektivität für Häuser oder kleine Unternehmen
- Unterstützung für VPNs, IPsec, etc.

## Weiterleitungstabelle
- Tabelle mit Präfix und Port
- größter Präfix wird verwendet (longest prefix matching)
- am besten mit effizienter Datenstruktur implementiert -> Trie

### Trie
Baumstruktur für Weiterleitungstabelle
- Looḱup O(W) (W ist Adresslänge)
- Speicherverbrauch O(N*W) (In der Praxis kleiner)
- Aktualisierungen O(W)

#### Path Compression
- Lookup O(W)
- Speicherverbrauch O(N)
- Aktualisierungen O(W)

### Hashtabelle
- Lookup in O(1)
- longest prefix matching funktioniert nicht -> als Cache für Trie verwenden

## Longest prefix matching
### Implementierung in Hardware
- RAM based access -> wird nicht verwendet

#### CAM based access
- TCAM (ternary content access memory) -> hat zusätzlich don't care (für Präfixvergleich)
- Präfixe sind sortiert in CAM (nach Länge)
- Lookup inherhalb eines Taktzyklusses
- Hoher Stromverbrauch
- Sehr teuer
- Geringe Dichte    
-> Skaliert nicht

## Routeraufbau
- Ein- und Ausgabeports
- Fabric zum verarbeiten der Daten

Blocking. Lösungen:
- Overprovisioning -> Fabric ist schneller als ports
- Buffering -> Warteschlange (entweder in Ports oder im Fabric)
- Backpressure -> Signal an Eingabeports
- Parallel switch fabrics -> Parallelverarbeitung, Zugang zu Ausgang muss schneller sein

### Puffer
An der Eingabe
- Durchsatz ist nicht sehr hoch (60%- 75%)

An der Ausgabe
- Speedup von N (Anzahl der Eingabeports) benötigt
- hoher Durchsatz möglich (80%-85%)

Im Fabric (distributed buffer)
- Matrizenstruktur
- nur Speedup von 1 nötig
- optimaler Durchsatz
- viel Speicher benötigt

Zentraler Puffer
- Weniger Verbrauch
- Braucht hohe Geschwindigkeit (1/2N)

### Fabric
z. B. als Bus realisiert
- für kleine Systeme
- Bus muss Geschwindigkeit aller Eingänge unterstützen (Summe)

Crossbar
- als Matrix alles mit allem Verbinden
- auch in eher kleinen Systemen

Multi level switching networks
- nicht volle Matrix implementiert

