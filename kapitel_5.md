# Software Defined Networking

Implementierung der Steuerungsebene in Software.

- SDN-Steuerung läuft als Software auf handelsüblicher Hardware
- Datenebene besteht aus simplen Paketprozessoren (SDN-Switche)

## Ablaufsteuerung
### Proaktive Flussprogrammierung
- Regeln werden bevor das erste Paket des Flusses ankommt programmiert
- normalerweise grobe, vordefinerte Entscheidungen
- keine Verzögerung für neue Verbindungen
- der Datenverkehr wird nicht aufgehalten, wenn keine Verbindung zur Steuerung besteht

### Reaktive Flussprogrammierung
- Regeln werden als Reaktion auf das Ankommen des ersten Pakets eines Flusses programmiert
- erlaubt feine Kontrolle auf Abruf
- kurze Aufbauzeit für jeden Datenfluss -> hoher Overhead für kurze Datenflüsse
- neue Datenflüsse können nicht konfiguriert werden, wenn die Verbindung zur Steuerung unterbrochen ist

## SDN-Arbeitsablauf in der Praxis
Die SDN-Steuerung kann Vergleichsregeln basierend auf
- den Eingangs-/Ausgangsport (`INPORT/OUTPORT`, `<portnum>`)
- die IP-Quelladresse (`IP_SRC`, `<ip-or-subnet>`)
- die IP-Zieladresse (`IP_DST`, `<ip-or-subnet>`)
- die Ethernet-Quelladresse (`MAC_SRC`, `<mac>`)
- die Ethernet-Zieladresse (`MAC_DST`, `<mac>`)
- den Ethernet-Rahmentyp (`ETHERTYPE`, `<hex>`)
- den UDP-Quell-/Zielport
- ...

in die Flusstabelle(n) der Switches packen.

Basierend auf den Regeln können dann Pakete
- verworfen (`DROP`)
- an alle anderen Ports geflutet (`FLOOD`)
- an den Controller gesendet (`CONTROLLER`)
- ausgegeben (`OUTPUT`, `<portnum>`)
- an eine weitere (indexmäßig höhere) Flusstabelle weitergegeben (`GOTO`, `<tablenum>`)
- verändert (`SET_FIELD`, `<fieldname>`, `<newvalue>`)

werden.

Zusätzlich kann die Priorität (`PRIORITY`) einer Regel (standardmäßig 1) geändert werden.

## Reagieren auf Ereignisse
`onConnect(switch)`
- bei einer neuen Kontrollverbindung zum Switch
- sinnvoll für proaktive Programmierung
- `switch` ist eine Referenz zum Switch (`switch.id` ist dessen ID)

`onPacketIn(packet, switch, inport)`
- wird aufgerufen, wenn ein Paket mit `r.ACTION('CONTROLLER')` weitergeleitet wurde
- `packet` ist das Paket, welches weitergeleitet wurde und gibt Zugang zu dessen Header-Feldern (`IP_SRC`, `MAC_DST`, `TTL`, ...)

Beispiel:
```
onConnect(switch) {
	r = Rule()
	r.MATCH('*')
	r.ACTION('CONTROLLER')
	r.PRIORITY(0) // lowest priority
	send_rule(r, switch)
}

onPacketIn(packet, switch, inport) {
	r1 = Rule()
	r1.MATCH('MAC_SRC', packet.MAC_SRC)
	r1.MATCH('MAC_DST', packet.MAC_DST)
	r1.ACTION('OUTPUT', getPort(packet.MAC_DST))
	send_rule(r1, switch)

	r2 = Rule()
	r2.MATCH('MAC_SRC', packet.MAC_DST)
	r2.MATCH('MAC_DST', packet.MAC_SRC)
	r2.ACTION('OUTPUT', getPort(packet.MAC_SRC))
	send_rule(r2, switch)
}
```

`send_packet(packet, switch, rule)`
- speist Paket in Switch ein
- `rule`: optionale Regel, welche anstatt der Flusstabelle verwendet werden soll

## OpenFlow
### Reservierte Ports
- `ALL`: Alle außer dem Eingangsport (flooding)
- `IN_PORT`: Sende Paket zurück
- `CONTROLLER`: Paket an Steuerung weiter leiten
- `NORMAL`: Kontrolle des Weiterleitungsprozess an die herstellerspezifische Switch-Implementierung abgeben

### Flusstabelle
Schema: Match fields | Priority | Actions | Counters | Timeouts | Cookie | Flags
- Counters: Anzahl der verarbeiteten Pakete
- Timeouts: Maximale Flusslebenszeit -> automatisches Entfernen von Flüssen
- Cookie: Markierung von SDN-Steuerung -> wird nicht bei der Paketverarbeitung verwendet
- Flags: Gibt an, wie ein Fluss verwaltet wird (z. B. Nachricht an Steuerung, wenn ein Fluss entfernt wird)

### Gruppentabellen
- Schema Gruppeneintrag: Group identifier | Group type | Counters | Action buckets
- weitere Weiterleitungsmethoden (link selection, fast failover)
- Gruppeneinträge können von anderen Tabellen über Gruppenaktionen (group actions) aufgerufen werden
 -> diese haben einen eindeutigen Gruppenbezeichner (group identifier)
- der Effekt von Gruppenprozessierung hängt vom Gruppentyp und den Aktionbehältern ab

#### Gruppentypen
- All: Alle Behälter einer Gruppe werden ausgeführt
- Select: Wählt einen von vielen Behältern einer Gruppe aus (z. B. Round-Robin oder Hashing)
- Fast failover: Führt den ersten "lebendigen" Behälter in einer Gruppe aus (lebendig=der zugehörige Port hat Verbindung)
- Indirect: Führt den einen Behälter einer Gruppe aus (austauschbar)

## Steuerungsverbindungen
### Out-of-band
- dedizierte (physische) Kontrollkanäle für Nachrichten zwischen Steuerung und Switch -> Kostenintensiv
### In-band
- Kontrollnachrichten gehen über den gleichen Kanal, wie der normale Datenverkehr -> die Steuerung muss sicher stellen, dass die Regeln nicht die Verbindung auf der Steuerungsebene beeinträchtigen

## Skalierbarkeit
- logisch zentralisierte Ansatz benötigt leistungsstarke Steuerungen -> mögliche Überlastung von Kontrollebene
- veschiedene Parameter für Implikationen bzgl. der Skalierbarkeit: Anzahl Switche, Endsystem, Datenflusse, Nachrichten; Verzögerung zwischen Switchen und Steuerung
- mögliche Lösung: Verteilte Steuerungen

### Verteilte Steuerungen
- Skalierbarkeit
- Zuverlässigkeit
- inkrementelles/modulares Deployment
- ...

## Limitierungen der Datenebene
### Konsistenz und Synchronisation
CAP-Theorem - zwei der folgenden Eigenschaften können zu einer Zeit erfüllt sein:
- Consistency
- Availability
- Partition tolerance

### Kapazität von Flusstabellen
- aktuell TCAM mit ca. 2000-20000 Einträgen möglich
- mögliche Lösungen: bessere Hardware; Workarounds wie Caching, Flussaggregierung und optimierte Verteilung von Regeln

### Latenzen der Flussprogrammierung
- Verarbeitungsdauer auf der Steuerungsebene
- Ausbreitungsverzögerung zwischen Switch und Steuerung
- Verarbeitungsdauer auf der Datenebene
- mögliche Lösungen: proaktives Einstellen wo möglich, schlaue Platzierung der Steuerung (schweres Problem)

## Werkzeuge und Programme
### OpenDaylight (ODL)
- SDN-Steuerung auf Produktionsniveau
- Java-basiertes Open-Source-Projekt, welches von der Linux-Foundation gehostet wird
- Beiträge von fast jeder großen Netzwerkorganisation
- Unterstützung für verschiedene Schnittstellen: OpenFlow, OVSDB, NETCONF, ...
