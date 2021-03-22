# Label Switching

Switching wird nicht nur Anhand der Zieladresse und des *für das Paket aktuell* kürzesten Pfades, sondern auch Anhand anderer, im Paketheader vorhanden, Daten betrieben.

Dies ermöglicht z. B. dass mehrere Pfade benutzt werden können oder das zusammengehörige Pakete den gleichen Pfad nehmen.

## Fluss (flow)

Ein (Daten-)Fluss ist eine abstrakte Sicht auf zusammengehörende Pakete.

Zusammengehörend sind dann z. B. Pakete
- von einer TCP-Verbindung
- mit einem bestimmten TCP/UDP-Port
- die eine HTTPS-Verbindung beschreiben
- von einer VoIP-Verbindung
- ...
- oder eine Kombination aus diesen Sachen.

-> Flussbasierte Weiterleitung ist Ebenenunhabhängig

### Micro-Flows
- beziehen sich auf eine "Verbindung"
- feine Kontrollmöglichkeiten
- große Anzahl von Flüssen möglich

### Macro-Flows
- höheres Aggregationsniveau
- Aggegierung von mehreren "Verbindungen"
- kleinere Anzahl von Flüssen

## MPLS
- "Multiprotocol Label Switching"
- verbindungsorientierte Kommunikation aufsetzend auf IP
- klare Separierung von Weiterleitung (label switching; Datenebene) und Steuerung (Manipulation von Label-Bindung; Steuerungsebene)
- das ursprüngliche Ziel war die schnellere Bestimmung des nächsten Spungknotens
- inzwischen wird es auch für "traffic engingeering" und VPNs verwendet

### Komponenten
- "Label-Switching Router" (LSR) -> kann IP und MPLS
- "Label-Edge Router" (LER) -> LSR mit nicht LSR-Nachbar; betreibt Klassifizierung

### Forwarding Equivalence Class
- Klassen für Pakete, die gleich behandelt werden sollen

### MPLS-Label
Struktur: 
- Label: The label itself
- Exp: Bits for experimental usage
- S: Stack-bit
- TTL: Time-to-live

### Protokoll für das Verteilen von Labeln
- ursprünglich: das, was mit MPLS definiert worden ist
- heute de-facto: RSVP-TE (Resource ReserVation Protocol)
