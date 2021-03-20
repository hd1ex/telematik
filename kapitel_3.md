# Internet Routing

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
