# Kapitel 2: Router

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

# Kapitel 3: Internet Routing

Routing ist
- für die Pfadbestimmung zuständig
- Teil des Kontrollpfades -> braucht Routingalgorithmen und -Protokolle

## Informationsebenen
Kontrollebene (control plane)
- Routingprotokolle
- Austausch von Routingnachrichten

Datenebene (data plane)
- IP Router schaut Metadaten an
- die Datenpakete werden über Layer 3 weitergeleitet

## Definitionen
### Routingtabelle (routing table)
- wird vom Routingprotokoll generiert
- enthält eine Abbildung von Ziel-IP-Präfixen zum nächsten Sprung (also eine IP-Adresse)
- ist für einen dementsprechenden Routingalgorithmus definiert
- keine Anforderungen an Performanz

### Weiterleitungstabelle (forwarding table)
- wird für die Paketweiterleitung verwendet
- enthält eine Abbildung von Ziel-IP-Präfixen zu ausgehenden Ports (Schnittstellenbezeichner und MAC-Adresse)
- optimiert für die Bestimmung des längsten Präfixes (longest prefix matching)
- die Performanz ist ein kritischer Aspekt (nachschauen in "line speed") -> dedizierte Hardware

### Routingmetrik
- Metrik, welche vom Router für Routingentscheidungen benutzt wird
- kann für einen individuellen Knoten oder für den ganzen Routingpfad benutzt werden
- Beispiele: Nutzung, Latenz, Datenrate, Anzahl von Sprüngen

### Routingrichtlinie
- Routingentscheidungen basierend auf Richtlinien
- Richtlinien werden vom Netzwerkbetreiber entschieden
- Beispiel: Ziehe Routen über spezifische Nachbarrouten vor

### Verteiltes adaptiertes Weiterleiten (distributed adaptive routing)
- aktuell am meisten verwendet im Internet
- eine Instanz des Routingprotokolls ist in jedem Router -> Routinginformationen werden via Routingnachrichten ausgetauscht
- Pfadanpassungen anhand der aktuellen Situation im Netzwerk

### Standardrouter (default router)
- wird normalerweise benutzt, um Endsysteme anzubinden

### Pfadberechnungen
Netzwerk ist als Graph modelliert:
- Graph G = (N, E)
- Router sind Knoten N (nodes)
- Verbindungen zwischen den Routern sind Kanten E (edges) und haben zugehörige Metriken

## Autonome Systeme
Das Internet, als Netzwerk von Netzwerken, ist in autonome Systeme (AS) aufgeteilt.
- zum Routing innerhalb eines autonomen Systems wird ein "Interior Gateway Protokoll" (IGP) verwendet
- zum Routing außerhalb eines autonomen Systems wird ein "Exterior Gateway Protokoll" (EGP) verwendet

Ein automnomes System
- wird über eine eindeutige Nummer, die "Autonomous Systems Number" (ASN), identifiziert (früher 16 bit; nun 32 bit)
- erscheint nach außen als eine einzelne Entität (auch bzgl. dessen Administration)
- hat einheitliche Routingrichtlinien
- hat normalerweise intern einheitliche IGPs -> versch. ASe können verschiedene IGPs haben

### Vorteile
- Aufteilung der Administrationsdomänen (dezentral)
- Skalierbarkeit, indem zwei logische Ebenen verwendet werden (IGPs und EGPs)

### Wichtige Eigenschaften
#### Skalierbarkeit von Routingprotokollen
Overhead erhöht sich mit der größe des Netzwerks:
- Speicherplatz für Routinginformationen
- Anzahl der auszutauschenden Routingnachrichten
- Rechenaufwand

#### Betreiberunabhängigkeit (autonomy)
- Auswahl des IGPs
- Verstecken/Kapselung der internen Netzwerkstruktur

### Zuordnung von AS-Nummern
Die IANA (internet assigned numbers authority) deligiert die Zuordnung an regionale Internetregistrare (RIR)
- ARIN (Nordamerika)
- RIPE NCC (Europa, Mittlerer Osten und Zentralasien)
- APNIC (Asien-Pazifik)
- LACNIC (Lateinamerika, Karibik)
- AfriNIC (Afrika)

Aktuell gibt es ca. 45.000 ausgestellte ASNs.

### Klassifikation von ASen
#### Rollenbasierend
Stummel-AS (stub AS)
- kleine Organisationen und Unternehmen (meist nur regional operierend)
- nur mit einem Anbieter verbunden
- kein Durchgangsverkehr

Mehrstandortiger AS (multihomed AS)
- große Firmen
- mit mehreren Anbietern verbunden (Zuverlässigkeit)
- kein Durchgangsverkehr

Durchgans-AS (transit AS)
- Anbieter
- oft globalen Umfangs

#### Basierend auf wirtschaftlichen Positionen/Einfluss
- Tier 1 (Transit-AS)
- Tier 2
- Tier 3 (Stummel- oder Mehrstandortige ASe)

#### Rollen
Endnutzer
- benutzt Internetanwendungen
- Beispiele:
  - Universitäten
  - Firmen
  - Kunden von ISPs: 1&1, KabelBW, AOL, Telekom, O2, Vodafone

Content-Lieferungs-Anbieter
- gefragt von Endnutzern/Internetanwendungen -> bieten Inhalt an
- Beispiele: Google, Akamai, Yahoo, YouTube, Facebook

### Ereichbarkeit über verschiedene autonome Systeme
Probleme
- Wie kann man die gegenseitige Erreichbarkeit gewährleisten?
- Zusammenarbeit zwischen autonomen Systemen?

Grundlegende Konzepte:
- Durchgangsverkehr (Transit) -> gekaufte Konnektivitäten
- Verkehrsaustausch (Peering) -> direkte Verbindung, normalerweise zwischen ASe der selben Ebene (Tier)

#### Erreichbarkeit und Durchgangsverkehr (connectivity and transit)
- Upstream: Anbieter (Verkäufer) von Verbindungsverkehr
- Downstream: Kunde (Käufer)    
-> hierarchische Struktur

Verkehrsaustausch (Peering)
- passiert in beide Richtungen
- nur der Downstream-AS muss zahlen; normalerweise eine Volumenrate

#### Privater Verkehrsaustausch (private peering)
- direkte Verbindung zwischen zwei ASen, normalerweise von gleicher Ebene (tier)
- keine Kosten für Verkehrsaustausch -> nur die Kosten für die Netzwerkinfrastruktur stehen an
- aber
  - meistens nur Datenverkehr zwischen privat austauschenden ASen
  - kein Durchgangsverkehr von anderen ASen

Vorteile:
- Sparen der Kosten
- kürzere Datenpfade (weniger Sprünge zwischen Quelle und Ziel)

Probleme:
- direkte Verbindungen von ASen schwierig (verschiedene Standorte)
- insgesamt wären (n-1)*n/2 Verbindungen nötig -> aktuell mehr als eine Millionen

#### Öffentlicher Verkehrsaustausch (public peering)
- "Intenet Exchange Points" (IXPs)
- zentrale öffentliche Autoritäten für Interverbindung
- neutrales Weiterleiten von Datenverkehr auf Ebene 2 (layer 2)
- keine Unterscheidung von Paketen (egal von welchem Kunden, Inhalt oder Art von Dienstleistung)
- Beispiele: DE-CIX, AMS-IX, LINX, Equinix
- Teilnehmer/Kunden zahlen eine monatliche Gebühr pro Netzwerkport

Richtlinien:
- offen: AS ist offen für Peering mit allen anderen ASen
- selektiv: AS betreibt Peering nur bestimmten Bedingungen und Auflagen
- restriktiv: AS akzeptiert keine neuen Peeringbeziehungen mehr
- kein Peering

Reale Umsetzung:
- normalerweise profitieren beide ASe gleich gut
- kein bedingungsloser Verkehrsaustausch ohne involvierte Kosten
- bei übermäßigem Verkehr: Abrechnung anhand der 95%-Regel (5% der höchsten Datenraten werden verworfen, um Verkehrspitzen abzufangen)

#### Peering-Ebenen (Tiers)
Tier 1
- große, globale ASe mit Zugang zu (allen) anderen ASen
- kaufen *keinen* Durchgangsverkehr
- verkaufen Durchgangsverkehr
- beitreiben Peering mit anderen Tier 1-ASen
- Beispiele: Deutsche Telekom, AT&T, NTT, Tata Communications, TeliaSonera

Tier 2
- große, nationale und inter-regionale ASe -> Verbindung zu Anbietern von Internetanwendungen
- Downstream von Tier 1-ASen
- verkaufen Durchgangsverkehr an andere ASe
- setzen normalerweise Peering ein
- Beispiele: Vodafone, Comcast, Tele2

Tier 3
- kleine, meist regionale ASe -> Verbindung zu kleineren Anbietern von Internetanwendungen
- Downstream von Tier 2-ASen
- verkaufen normalerweise keinen Durchgangsverkehr an andere ASe
- verkaufen überwiegend Durchgangsverkehr an Endkunden/-nutzer
- setzen normalerweise Peering ein
- Beispiele: KabelBW, NETHINKS, Alice, Congstar, Versatel

#### Content-Lieferungs-Anbieter
- wollen eine möglichst niedrige Latenz erzielen
- präferieren Standorte nahe der Peeringpunkte der Tier 1-Anbieter

Zwei grundlegende Alternativen:
- Webserver sind direkt bei Tier 1-ASen gehosted -> keine AS-Nummer benötigt
- Webserver sind über eigene Router verbunden
  - "Content Delivery Network" (CDN)
  - eigene AS-Nummer benötigt
  - Peering mit essentiellen Anbietern an wichtigen Knotenpunkten
  - Beispiele: Google, Akamai, Yahoo

#### "Content Delivery Network" (CDN)
- weltweites Netzwerk mit eigener AS-Nummer
- tausende "Points of Presence" (PoP) verteilt auf die ganze Welt

Ziele
- Lastverteilung an Zugriffsroutern (auch Wahl des Besten)
- nahe an Kunden sein

### Routing in und zwischen autonomen Systemen
- IGPs basieren auf Metriken
- EGPs basieren auf Richtlinien

Am meisten verwendet sind
- RIP (Routing Information Protocol) -> basiert auf Distanz-Vektor-Algorithmus
- OSPF (Open Shortest Path First) -> basiert auf Verbindungszustandsalgorithmus

Zusätzlich sind auch noch in Verwendung
- IS-IS (Intra-Domain Intermediate System to Intermediate System Routing) -> basiert auf Verbindungszustandsalgorithmus; wird von großen Anbietern verwendet
- EIGRP (Enhanced Interior Gateway Routing Protocol) -> proprietäre CISCO-"Erweiterung" von RIP, um "count-to-infinity" zu lösen

## RIP: Routing Information Protocol
- ist ein IGP
- einfach
- eines der Ersten
- braucht wenig Konfiguration
- RIPv2 ist die aktuelle, wirkliche Version
- Anwendung (z. B. routed) implementiert RIP und verwaltet Weiterleitungstabelle
- RIP-Routing-Nachrichten werden über UDP versendet (nicht zuverlässig)

### Routing-Metrik
- Distanz zwischen Quelle und Ziel entspricht der Anzahl an Sprüngen im Pfad (hop count)
- "Hop count" ist zwischen 1-15 (16 entspricht unendlich)

### Routingnachrichten
- Request: Anfragen der gesamten Routingtabelle oder ein Teil dieser
- Response: Antwort auf
  - bestimmte Abfrage
  - reguläre Aktualisierungen: Broadcast alle 30 Sekunden
  - ausgelöste Aktualisierung: Metrik einer Route hat sich geändert

#### Ausgehend
##### Reguläre Aktualisierungen
- ganze Routingtabelle wird an alle Nachbarn gesendet
- Einträge werden periodisch aktualisiert
- bei keiner Aktualisierung für min. 180 Sekunden -> "Hop Count" wird auf 16 gesetzt und damit die Route invalidiert

#### Ausgelöste Aktualisierungen
- nur Änderungen werden kommuniziert
- ratenlimitiert, um Netzwerklast zu verringern -> zufälliger Wert zwischen 1 und 5 Sekunden, Änderungen werden akkumuliert

#### Eingehend
- Eintrag für Zieladresse existiert noch nicht in der Routingtabelle und die empfangene Metrik ist nicht unendlich -> neuen Eintrag einfügen
- aktueller Eintrag für Zieladresse hat größere Metrik oder Aktualisierung kommt vom "nächsten Router" für dieses Ziel -> Eintrag ändern
- ansonsten -> Routingaktualisierung ignorieren

## OSPF: Open Shortest Path First
- IGP
- Verbindungszustandsalgorithmus -> jeder Router im Netzwerk muss die gesamte Topologie des Netzwerks lernen
- jeder Router berechnet für sich den kürzesten Pfad (Dijkstra)
- OSPF setzt auf IP auf (nicht zuverlässig)
- jeder Router lernt seine Nachbarn kennen und überwacht die Zustände der Verbindung zu denen

### Verbindungsstatusse eines Routers
- Router-IDs von Nachbarn  - dynamisch erkundet mit dem Hello-Protokoll
- Verfügbarkeit  - dynamisch erkannt durch Hello-Protokoll oder in der physischen Ebene
- alles andere ist vorkonfiguriert

### Vorkonfiguration eines OSPF-Routers
- Router-ID (einzigartig für Router im Netzwerk) -> z. B. kleinste IP-Adresse seiner Schnittstellen
- Parameter pro Schnittstelle
  - Schnittstellen-IP-Adresse (und Maske)
  - Schnittstellen-Ausgangskosten (Metrik) -> normalerweise inveres proportional zur Schnittstellendatenrate

### Routing-Metrik
- Beispiel: Kosten = Referenzdatenrate/Schnittstellendatenrate, wobei die Referenzdatenrate konfigurierbar und konsistent sein sollte (bspw. 100 Mbit/s)

### Link State Advertisment (LSA)
- LSAs beinhalten Informationen über die Nachbarn und Verbindungen eines Routers
- LSAs werden per Broadcast an alle Router gesendet
- LSAs haben eine maximale Lebenszeit (1h) und müssen alle "LSRefreshTime" ersetzt werden (30min, mindestens 5s)

### Verbindungszustandsdatenbank
- speichert die neusten LSAs von allen anderen Routern im Netzwerk
- darauf basierend wird der Topologiegraph des Netzwerks und die Routingtabelle berechnet
- initial wird diese durch Anfrage nach dem Handschlag beim Hello-Protokoll übermittelt

### Hello-Protokoll
#### Ziele
- Sicherstellen der bi-direktionalen Kommunikation zwischen benachbarten OSPF-Routern
- Herstellen und Erhalten von logischen Nachbarschaften

#### Inhalt
- eigene Router-ID
- Router-ID von benachbartem Router, wenn bekannt (ansonsten 0.0.0.0)
- Zieladresse ist 224.0.0.5 (Multicast für "AllSPFRouters")

#### Ablauf
- Router sendet periodisch eine Hello-Nachricht an all seine Verbindungen
- wenn keine Hello-Nachricht nach einer vordefinierten Zeit empfangen wird (10-30s), dann wird die Verbindung als unterbrochen angesehen

### OSPF-Nachricht
- Header: Version | Type | Packet Lenght | Router ID | Area ID | Checksum | AUType | Authentification
- Version: aktuell 2 für IPv4 und 3 für IPv6
- Type: Hello; database description; link state request; link state update; link state acknowledgement
- Router ID: ID des ürsprünglichen Routers
- Area ID: OSPF-Bereich (siehe unten)
- Checksum: Internetprüfsumme über die ganze OSPF-Nachricht
- AUType und Authentification: Optionale Authentifizierung des ürsprünglichen Routers

### LS-Update-Nachricht
- Header: Anzahl LSAs | LSAs ...

### Struktur von LSAs
- Header enthält Informationen für die eindeutige Identifizierung des LSAs
  - Werbender Router
  - Sequenznummer von LSA am werbenden Router
  - ...
- Body enthält Informationen über alle operierenden Schnittstellen des Routers
  - verbundene Kosten
  - Verbindungsart (zu einem anderen Router, zu einem Endsystem, ...)
  - Erreichbarkeitsinformationen (ID von anderem Router, IP-Adresse von Endsystem)

Aufbau: LS Age | Options | LS Type | Link State ID | Advertising Router | LS Sequence Number | Checksum | Length | ... | Number of Links | ... information for each link

#### LSA-Header
- LS Age: Zeit in Sekunden seit LSA-Erstellung
- Options: Optionale Fähigkeiten, die von der OSPF-Domäne unterstützt werden
- LS Type: Router-LSA, Netzwerk-LSA ...
- Link State ID: Identifiziert ein LSA eindeutig
- Advertising Router: OSPF-Router-ID vom ursprünglichen Router
- LS Sequence Number: Wird jedes Mal erhöht, wenn ein neues LSA generiert wird
- Checksum: Über die gesamte Nachricht außer dem "Age"-Feld
- Lenght: Anzahl Bytes der gesamten LSA mit Header

### Ausstellen von LSAs
- wenn sich nichts verändert, muss auch nichts gemeldet werden -> schweigen
- LSAs werden alle 30min erneuert
- bei Änderungen, wie
  - Schnittstellen kommen dazu oder fallen weg
  - beachbarter Router an Schnittstelle ist unereichbar
  - Konfigurationsänderungen
- die minimale Zeit zwischen zwei aufeinanderfolgenden LSAs eines Routers ist auf 5 Sekunden gesetzt (aus Stabilitätsgründen)

### Synchrone LS-Datenbanken
Es muss sicher gestellt werden, dass
- jedes LSA von jedem Router empfangen wird -> zuverlässiges "flooding"
- alle Router konsistent einen LSA entweder speichern oder weg werden -> deterministische Vergleichsregeln
- ausgelaufene LSAs aus jeder Datenbank gelöscht werden -> Uhren sind ggf. nicht synchron, daher "flooding" auch bei ausgelaufenen LSAs mit spezieller Behandlung von "MaxAge"-LSAs

#### Umsetzung
- es muss ein zuferlässiger "flooding"-Algorithmus verwendet werden
- es wird mit ACKs gearbeitet
- die Sequenznummer und das Altersfeld werden dazu verwendet

### OSPF-Gebiete
- Problem: OSPF alleine skaliert nicht
- AS wird in Gebiete eingeteilt
- Gebiete teilen die zusammengefassten Informationen miteinander
- typische Größe von Gebieten ist ca. 100 Router
- "Area 0" ist das Backbone des AS -> mit dem muss man immer verbunden sein
- alle anderen Gebiete sind mit Backbone verbunden
- "Area border routers" (ABRs) verbinden Gebiete -> sind Teil beider Gebiete und haben zwei OSPF-Instanzen
- ABRs generieren zusammenfassende LSAs für das zugehörige Gebiet

## RIP vs OSPF
RIP
- hat nur eine Metrik (Sprunganzahl)
- hat nur eine max. Pfadlänge von 15 Sprüngen
- hat alle 30 Sekunden Aktualisierungen, auch bei keinen Änderungen
- hat eine langsame Konvergenz und count-to-infinity
- ist deswegen nicht für große Netzwerke geeignet
- ist aber einfacher und braucht weniger Ressourcen als OSPF
- wird manchmal noch in kleinen Netzwerken genutzt

OSPF
- addressiert die Nachteile von RIP: schnellere Konvergenz, keine count-to-infinity, weniger Signal-Overhead
- große Netzwerke können in Gebiete aufgeteilt werden
- Standard in großen ASen (zusammen mit IS-IS)

## BGP
- das wichtigste EGP
- ein Pfadvektor-Protokoll (Erweiterung von Distanzvektor)
- EBGP: Zwischen BGP-Routern benachbarter ASe -> Pfadinformationen
- IBGP: Zwischen BGP-Routern innerhalb eines AS -> Synchronisation von Transit-Routen

### Routing mit BGP und IGPs
#### Standardrouten
- Pakete mit unbekannter Adresse/Präfix werden zu einem "Default"-BGP-Router geleitet
- Gut für Stummel-ASe, aber nicht praktikabel für Transit-ASe

#### Veröffentlichung externer Routen über IGP
- erlaubt mehr Kontrolle
- nicht mit allen externen Routen möglich (Skalierbarkeit!)
- normalerweise mit Standardrouten kombiniert

#### IGP-Router spricht auch BGP
- die Weiterleitungstabelle wird aus zwei Routingtabelle aufgebaut (BGP + IGP)
- wird oft bei großen Backbone-Anbietern verwendet

### BGP-Nachrichten
- OPEN: Aufbauen der BGP-Verbindung (TCP-Verbindung existiert schon), Authentifizierung
- UPDATE: Bekanntgeben eines neuen oder Zurückziehen eines veralteten Pfades
- KEEPALIVE: Bei Absenz von UPDATE-Nachrichten, Timer z. B. auf 30 Sekunden
- NOTIFICATION: Fehlermeldung und Abbrechen der BGP-Verbindung

### Routing Information Base
- Adj-RIB-In: Existiert pro Partner, speichert empfangene Informationen von diesem Partner
- Loc-RIB: Die wirkliche Routingtabelle, nur wirklich präferierte Routen zu Zielnetzwerken sind hier drinnen -> daraus wird die "Forwarding Information Base" (FIB) gebaut
- Adj-RIB-Out: Existiert pro Partner, enthält Routen, welche an diesen Partner veröffentlicht werden
# Kapitel 4: Label Switching

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
# Kapitel 5: Software Defined Networking

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
- verschiedene Parameter für Implikationen bzgl. der Skalierbarkeit: Anzahl Switche, Endsystem, Datenflusse, Nachrichten; Verzögerung zwischen Switchen und Steuerung
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

# Kapitel 6: Network Function Virtualization

## Middlebox
Ein Gerät des Datenpfades zwischen einer Quelle und einem Ziel, welches Funktionen ausführt, die über die eines normalen, standard-IP-Router hinaus gehen.

## Network Function
Eine Funktionalität, die von einer Middlebox auf dem Datenpfad ausgeführt wird.

Beispiele: NAT, firewall, proxy, load balancing, intrusion detection, etc.

### Network Address Translation (NAT)
Verbindet einen Netzwerkbereich mit **privaten Adressen** mit einem Netzwerkbereich aus **globalen, eindeutigen Adressen**.
- Problem: private Adressen können nicht fürs Internet-Routing verwendet werden
- Klienten im privaten Adressbereich teilen sich globale, eindeutige Adressen -> **Austauschen von Adressen** nötig

### Firewall
Überwacht und steuert einkommenden und ausgehenden Datenverkehr
- Barriere zwischen vertrauenswürdigen und nicht vertrauenswürdigen Netzen
- Leitet weiter oder verwirft Pakete basierend auf einer vordefinierten Regelmenge
- shallow packet inspection: Entscheidungen nur auf Feldern im **Header**
- deep packet inspection: Überwacht auch den Inhalt von Datenpaketen
- Zustandslos: Einzelne Pakete werden unabhängig voneinander betrachtet
- Zustandsbehaftet: Zustand wird zwischen Paketen behalten

### Caching
Zwischenpuffern von Inhalten.

### Klassische Middleboxes
Früher mit proprietärer Hardware implementiert.

## Network Function Virtualization (NFV)
Ideen vom "cloud computing" nachahmen:
- Netzwerkfunktionen in Software implementieren
- Hardwareunabhängigkeit mittels Virtualisierung
- Verwendung von leistungsstarker Server-Hardware

Vorteile:
- Teilen von Ressourcen
- Agilität und Flexibilität
- schnelles Deployment
- verringerte Kosten

### Hauptbestandteile von NFV
- Virtualized Network Functions (VNFs)
- NFV Management and Orchestration (MANO)
- NFV Infrastructure (NFVI)

## Virtualisierung
Softwareabstraktionschicht zwischen Hardware und VMs -> Hypervisor

### Typ 1 Hypervisor
läuft direkt auf der Hardware

### Typ 2 Hypervisor
läuft als Userspace-Programm direkt über dem Kernel

### Containerbasierte Virtualisierung
Ein Kernel stellt mehrere Instanzen (Container) des Host-OS bereit.

### Hypervisor vs. Container
Docker ist sehr viel besser als KVM.

## Service Function Chain
- geordnete Menge von Netzwerkfunktionen -> Reihenfolge wird auf Datenfluss angewendet
- Beispiele: Firewall -> Auth-Server; Load balancer -> Cache
- z. B. mit MPLS umgesetzt

## Herausforderungen von NFV
- Sicherheit
- Performanz
- Standortwahl
- Zuverlässigkeit
- Testen und Fehlerfindung
- Anforderungen auf Netzwerkbetreiber-Niveau
- Co-Existenz mit Legacy-Netzwerken

# Kapitel 7: Internet Congestion Control

## Geteilte (Netzwerk-)Ressourcen
Übergeordnete Ziele in Bezug auf Netzwerke:
- eine **gute Auslastung** von Netzwerkressourcen bereit stellen
- eine **akzeptabele Performanz** für Benutzer bereit stellen
- **Fairness** zwischen Benutzern / Datenströmen bereit stellen

Mechanismen, welche mit geteilten Ressourcen handhaben, sind
- Scheduling
- Medienzugriffskontrolle
- Staukontrolle (Congestion control)

## Problem
Staukontrolle
- verändert die Last, um Überlastsituationen zu vermeiden
- benutzt Feedback-Informationen

## Puffer
- Router benötigen Puffer (Warteschlangen), um temporäre Verkehrsspitzen abzufangen
- Wenn Puffer voll sind, müssen die Pakete verworfen werden

## Throughput vs. Goodput
- Throughput: Übertragene Datenrate
- Goodput: Datenrate auf Anwendungsebene

## Knee and Cliff
- Knee: Datenlast, bei der angefangen wird zu Puffern
- Cliff: max. Last, bevor Pakete verworfen werden

## Window-based Congestion Control
Fenster für Staukotrolle (CWnd)
- Bestimmt die max. Anzahl von nicht-bestätigten Paketen pro TCP-Verbindung
- passt die Senderate der Quelle zur Bottleneck-Kapazität an -> selbsttaktend

## Rate-based Congestion Control
Die Senderate wird limitiert.

## Implizite vs explizite Stausignale
Implizit: Ohne dedizierte Unterstützung vom Netzwerk
- Timeout vom "retransmission timer"
- Empfang von doppelten ACKs
- RTT-Variation

Explizit: Knoten innerhalb des Netzwerks signalisieren Stau
- ECN und DCTCP (später)
- Ethernet congestion control

Im Internet: Normalerweise keine Unterstützung für explizite Stausignale
- Staukontrolle muss mit impliziten Stausignalen funktionieren
- explizite Meldungen nur komplementär

## End-to-end vs Hop-by-hop
- End-to-end: Von Endsystem zu Endsystem
- Hop-by-hop: Von Sprung zu Sprung (zwischen Knoten)

## Verbesserte TCP-Versionen
Ziele:
- verfügbare Netzwerkkapazität schätzen, um Überlastungen zu vermeiden -> Feedback geben
- den Verkehr dementsprechend Regulieren -> Anwenden der Staukontrolle

## TCP Tahoe
benutzte **Mechanismen** für Staukontrolle:
- langsames Starten
- Timeout
- Stauvermeidung
- "Fast retransmit"

Stausignal
- Timeout oder
- Empfangen von doppelten ACKs

Gleichung muss immer erfüllt sein:
```
LastByteSent - LastByteAcked ≤ min{CWnd, RcvWindow}
```

### Variablen
- `CWnd`: Kontrollfenster
- `SSThres`: Threshold für langsamen Start

### Einfacher Ansatz
AIMD: additive increase, multiplicative decrease von `CWnd`

### Stausignal
- ACKs werden normalerweise immer für die *nächste* Rahmennummer, die noch zu empfangen sind, gesendet (z. B. 30 empfangen, sende ACK mit Nummer 40)
- erneut Senden nach dem Empfang von 3 gleichen ACKs

## TCP Reno
Neues Konzept: Unterscheiden zwischen
- großem Stausignal -> Timeout
- kleinen Stausignal -> duplizierte ACKs

Beim Empfang von einem kleinen Stausignal
- kein Reset auf langsamen Start
- fast recovery

Beim Empfang von einem großen Stausignal
- setze auf langsamen Start zurück (wie bei TCP Tahoe)

## Analyse der Verbesserungen
### Selbsttaktung
TCP pendelt sich selbst durch das nutzen von Fenstern ein.

### Langsamer Start
- für den Anfang der Verbindung (da dort kein Takt vorhanden ist)
- bei Vermutung von Stau
- für hohen Goodput zuständig (ohne nur 35% beim Einpendeln)

### Retransmission Timer
- Schätzung der Rundreisezeit (RTT)
- `EstimatedRTT = (1 − 𝛼) * EsitimatedRTT + 𝛼 * SampleRTT`
- Timeout `RTO = 𝛽 * EstimatedRTT`
- übliche Werte: `𝛼=0,125; 𝛽=2`

#### Multiple Retransmissions
Problem 1: Wie groß sollte das Interval zwischen 2 aufeinanderfolgenden Erneutsendungen sein   
Ansatz: Exponentieller Backoff -> `RTO = 2 * RTO` (mit Obergrenze, z. B. von 60s)

Problem 2: Zu welchem Segment gehört ein empfangenes ACK - zum Originalen oder zum erneut Gesendeten?    
Ansatz: Karns Algorithmus
- ACKs von erneut gesendeten Segmenten werden nicht in die Berechnung der geschätzten RTT und Abweichung miteinbezogen
- Backoff wird wie vorher berechnet
- Timeoutwert wird mit dem Backoff-Algorithms berechnet bis ein ACK eines nicht erneut gesendeten Segments empfangen wurde
- danach wird der ursprüngliche Algorithmus wieder eingesetzt

### Stauvermeidung
- bei neuer TCP-Verbindung muss die Last von Bestehenden gesenkt werden
- mittels AIMD

## Optimierungskriterien

### Netzwerklimitierter Sender
Senderate ist durchs Netzwerk (bzw. dessen Bottleneck) limitert.

### Applikationslimitierter Sender
Senderate ist durch Applikation limitert.

### Fairness
Alle Sender, die das Bottleneck teilen, bekommen einen fairen Anteil.

#### Jains Fairness-Index
Fairnesszahl ist die quadrierte Summe geteilt durch N mal die Summe der Quadrate der Datenraten. 

#### Max-min Fairness
- Benutzer können sagen, welche Datenraten sie benötigen
- Datenraten sind minimal der angeforderte Bedarf (wenn dieser Fair gedeckt werden kann)
- und maximal `MAXRATE / N`, wobei `MAXRATE` die max. mögliche Datenrate der Verbindung ist, die aufgeteilt wird
- nicht angeforderte Datenraten kommen auch in den Pool für `MAXRATE`

## Active Queue Management (AQM)
Beim Erkennen von Staus *frühes Feedback* and Sender geben
- zufällig Pakete verwerfen
- oder ein explizites Stausignal senden

### Random Early Detection (RED)
Ansatz:
- `durchschnittliche Warteschlangenbelegung < qmin` -> keine Segmente verwerfen
- `qmin <= durchschnittliche Warteschlangenbelegung < qmax` -> Wahrscheinlichkeit für das Verwerfen eines einkommenden Pakets wird linear mit der durchschnittlichen Warteschlangenbelegung erhöht
- `durchschnittliche Warteschlangenbelegung >= qmax` -> alle einkommenden Segmente verwerfen 
- Problem: Wie setzt man die RED-Parameter `qmin, qmax, maxp`?

### Explizit Congestion Notification (ECN)
- markiere IP-Paket, welches zum Empfänger weiter geleitet wird
- der Empfänger schaut, dass das Fenster beim Sender dementsprechend angepasst wird
- über zwei Bits im ECN-Feld vom IP-Header: ECT - ECN-capable Transport (gesetzt vom Sender); CE - Congestion experienced (gesetzt vom Router)
- Nachrichten in Ebene 4 (TCP):
  - ECE bit (ECN echo): Nachricht an Sender, welche im ACK mitgeführt wird
  - CWR bit (Congestion window reduced): Signalisiert Empfang von ECE an TCP-Empfänger
  - wird als kleines Stausignal interpretiert

# Kapitel 8: Ethernet Evolution
## Aloha
Erstes MAC-Protokoll für paketbasierte, kabellose Netzwerke.    
Ein Media Access Control:
- Zeitmultiplexing, variabel, zufällige Zugriffe
- *asynchroner* Zugriff
- Problem: Kollisionen (Erkennung und Reaktion)
- max. Auslastung: 18,4%

## Slotbasiertes Aloha
Wie Aloha, aber
- benutzt Zeitslots -> synchroner Zugriff
- durchnittlich weniger Kollisionen als Aloha
- Anwendungsbeispiel: GSM (Kontrollkanal)
- max. Auslastung: 36,8%

## CSMA-basierte Ansätze
CSMA - Carrier Sense Multiple Access

CSMA/CD
- .../"Collision Detection"
- Zuhören vor dem und während dem Senden
- Kollisionen werden durch Zuhören erkannt
- Anwendungsbeispiel: Ethernet

CSMA/CA
- .../"Collision Avoidance"
- Sendesytem vermutet Kollision, wenn ein ACK fehlt
- Anwendungsbeispiel: WLAN

## Ethernet
- Datenrate: 10 Mbit/s
- Sende Jamming-Signal, wenn eine Kollision erkannt wurde
- und wende exponentiellen Backoff an
- braucht minimale Sendezeit (Rahmengröße), um CD anzuwenden

## Fast Ethernet
- Datenrate: 100 Mbit/s
- Netzwerktopologie: Stern

## Ethernet-Flusskontrolle
- Ziel: Vermeide Paketverluste durch Pufferüberläufe
- Ansatz: Reduziere den gesendeten Verkehr zum Switch

### Implizite Flusskontrolle
Bei Halbduplexverbindungen möglich:
- Kollision erzwingen
- blockiere selber den Kanal

### Explizite Flusskontrolle
Bei Vollduplexverbindungen möglich:
- PAUSE-Rahmen wird bei Überlast gesendet
- Wartezeit wird mitgesendet (bei Zeit=0 wird das Senden später wieder explizit gestartet)

### Zwischenebene für MAC-Steuerung
- Steuerungsrahmen sind Teil der Zwischenebene (also z. B. der PAUSE-Rahmen)

## Gigabit Ethernet
- Datenrate: 1 Gbit/s
- Netzwerktopologie: Stern

### Frame Bursting
Senden von mehrereren kurzen Rahmen am Stück.

## 10/40/100 Gigabit Ethernet
- nur noch Punkt-zu-Punkt-Verbindungen
- nur noch Vollduplexverbindungen
- das Format des Ethernetrahmens wurde beibehalten

## Bridges
- Ziel: Verbinde LANs auf Ebene 2

### Source-Routing bridges
- Endsysteme fügen Weiterleitungsinformationen zu Sendepaketen hinzu
- Bridges leiten die Pakete basierend auf diesen Informationen weiter
- Endsystem muss den Pfad kennen (nicht transparent)

### Transparent Bridges
- lokale Weiterleitungsentscheidungen in jeder Bridge
- Weiterleitungsinformationen in Weiterleitungstabelle gespeichert
- statische und dynamisch erlernte Einträge möglich
- Endsystem ist nicht in die Weiterleitung involviert
- wird oft in der Praxis benutzt (z. B. Switches)

#### Grundlegende Aufgaben
- Aufbau einer zyklusfreien Topologie -> Spanning-Tree-Algorithmus
- Weiterleiten von Paketen

#### Anforderungen für die Benutzung des Bridge-Protokolls
- Gruppenadresse (MAC-Adresse)
- eindeutige Bridge-IDs
- eindeutige Schnittstellen-IDs
- Pfad-Kosten

#### Bridge Protocol Data Units (BPDUs)
Beinhaltet
- ID der sendenden Bridge
- ID der angenommenen Wurzel-Bridge
- Pfad-Kosten von der sendenden Bridge zu der Wurzel-Bridge

