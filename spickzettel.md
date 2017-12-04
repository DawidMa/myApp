# L1
DMA wichtig für virtualisierung

## Interrupt
- Früher in Mikrocontrollern
- Ein laufendes Programm wird unterbrochen, anderer Code wird ausgeführt
- "Return from interrupt" kommt zurück zum pausierten Code
- Es gibt mehr als ein Interrupt auf einmal, in normalen BS nicht, nur in manchen MC (Microcontrollern)
- mit INTA wird gemerkt welcher 

# L2
## Binden von Modulen
- Notwendigkeit dass ein Programm mit korrekten Adressen arbeitet
- immer wenn man bindet muss man relativadressen anpassen

## Addressierungsmechanismen: Partitionierung
- so wie sie auf der Platte liefen sind sie lauffähig
- jede partiotion ist ein eigenes Segment
- beim laden in hauptspeicher werden adressen umgerechnet
- Partitionierungen waren fix und somit viel verlust/verschnitt => ausgestorben

## Virtuelle Adressierung(VA)
- mit MMU
- Virtuelle Adresse (32bit), man will zusätzliche funktionen
- einfach verwalteter Hauptspeicher, wenig verschnitt, dynamisch
- virtuelle in reale umgewandelt, hier in tabelle rausnehmen und echte adresse bekommen
- 12345 -> 4E345. brauchen nur die linken also 1 und 2
- pageindex  18 und in der tabelle den eintrag von hex suchen !!
- meist 4kb große pages
- PW: present und writable
- Programm darf nur mit interrupt verlassen werden, ansonsten nicht sicher
- jeder Prozess hat eigene seitentabellen, hierfür 2 stufige VA
- Heap und stack wachsen und schrumpfen, wachsen aufeinander zum

## Page Table Entry
- PWUTDAM

### Adressierung mit 4MB Pages
- dabei entsteht 2MB verschnitt pro zuteilung

### 64-Bit Prozessoren
- wenn p=0 braucht man die anderen Tabellen nicht

### invertierte Seitentabellen
- ohne MMU
- es gibt nur TLB (einer virtuellen wird reele Adresse zugeordnet (als Tabelle))
- zuordnung wird ausgerechnet
- wenn TLB-Miss dann wird nicht MMU veranlasst seiten zu lesen sondern es gibt ein Interrupt und lädt
  TLB
- sehr kleine Tabelle. Pro reeler Seite nur ein Eintrag
- wenn RAM 2GB groß dann braucht man bei 4K pages 500 Einträge
- pid gehört nicht zur adressierung
- pid wird einfach durchsucht

#### Translation Lookaside Buffer (TLB)
- um die 1000 Einträge
- man braucht schnellen zwischenspeicher
- TLB ist ein cache mechanismus
- Prozessor läuft über Tabellen. anfangs TLB leer
- wenn voll gibt es verdrängungsstrategie

# L3
## Softwarestruktur
- man wollte dialogorientierung: prozessoe bekommen prozessor wen nsie ihn brauchen
- rechner "erfüllt jeden Wunsch" als wäre es nur ihr rechner
- mehrere Anwendungnen meist quasi-parallel
- speicherzugriffe sequentiell
- Anwendung kann nicht in den adressraum anderer Anwendung rein

## Entstehung von Prozessen
- Prozess kann man kopieren mit "fork()", dabei entsteht neue Pagetable
- es gibt Vater und Sohn, dadurch leichter Ordnung zuhalten
- wenn Vater tot dann bekommen Kinder Signal dass Zeuger gegangen ist, Defaultreaktion ist: beenden
  z.b. wenn Benutzer geht soll alles aufhören
  
## Threads
- statt komplett neue Prozesse kann man neue Handlungsfäden im Prozessraum erschaffen
- CPU entweder immer in anderen Thread geschickt oder bei mehrkernern gleichzeitig
- jeder Thread neuer Stack
- Threads können sich gegenesitig in Stack hineinkucken, wenn Thread1 an Thread2 was abgibt

### Erzeugung
- innerhalb laufendem Programm mit Aufruf
- nicht limitiert, solang BS mitmacht

### Arten
- Kernel Level- und User Level-Threads
- ULT: weiß nicht dass weitere erzeugt werden
- KLT: vom BS erzeugt und verwaltet 

## Prozesszustände
- wartend: erst raus wenn Ereigniss zutrifft auf welches man wartet
- geladen, bereit, rechnend, ausgelagert, nicht existent..

## Aufteilung des VA
- wenn Prozessgröße über 3GB will, geht es nicht -> Fehler
- dient dazu, dass BS dort eingeblendet werden kann

## Prozessverwaltung
- welche Prorität hat der Prozess
- wie lange hat er schon gerechnet? wann wurde er erzeugt? wem gehört er? ..

## Scheduling
- wer kommt wann dran?
-> Folien

### Arten
- nicht-präemptiv: während Laufzeit nicht unterbrochen
- Präemptiv: Prozess kann jederzeit Prozessor entzogen werden mit Interrupt
- First in first out
- Shortest job first (alt)
- Round Robin: jeder kommt dran bis Zeitscheibe weg ist, wenn Prozess fertig kommt der nächste direkt dran
- Multilevel Scheduling: nur hiermit kann man Reaktionszeiten garantieren, mit Prioritäten -> Echtzeit
- Multilevel-Feedback-Scheduling: Priorität kann gewechselt werden nachdem Zeitscheibe ausgenutzt

# L4
## Demand Paging
- Wichtig ist moment der Zeitenzuteilung
- Speicher angelegt mit aloc()
- bei Execption: Hab ich ihm Speicher noch nicht gegeben? -> p-bit setzen
- Seiten werden erst gegeben wenn gebraucht und nicht wenn gefordert

## Copy on Write
- Sohn kann alle Variablen des Vaters benutzen, Vater hat die alten -> werden kopiert
- erst wenn modifiziert wird kopie ersttellt
- bei fork() Ressourcenoptimal

## Seitenverdrängung
- man kann Prozessen Seiten wegnehmen
- wenn Speicher knapp wird 
- Risiko weil man Seiten wegnimmt und hofft dass er sie nie mehr braucht (trifft oft zu)
- Prozess kann sie auch wieder verlangen, aber gibt dafür eine wieder aber
- Rechenbeispiel auf Folie

### FIFO
- jede Seite als Schlange verwaltet, ohne Zeitstempel

### Clock-Algorithmus
- a-bit signalisiert: diese Seite wurde benutzt (Access)
- man sucht Seiten auf die nicht zugegriffen wurde. Werden aber nur geladen wenn genutzt. Darum haben alle das a-bit
- dann sucht man welche wurden lange nicht benutzt
- auch subotpimal

### Least Recently Used
- Alterungs-/Häufigkeitszähler
- am längsten nicht benutzte Seite nimmt man dann

### Thrashing
- Seiten sind so groß, dass man beim einlagern auch auslagern muss
- nichts geht mehr..

# L5
## Kommunikation von Prozessen
- wenn Daten austauschen müssen sie um Hilfe bitten

### Erzeuger Verbraucher Problem
- einer schickt und anderer bekommt Daten
- wenn Erzeuger schneller erzeugt als verbaucht wird, dann können Datenverluste entstehen
- man braucht Mechanismus der Erzeuger anhält ohne dass er es merkt
- Zweite Kommunikationsschine oder automatisch Ablauf synchronisiert

## Pipes
- Kommunikationsschine zwischen zwei Prozessen
- Schreiber oder Leser angehalten wenn zu schnell (Pipe leer)

### Arten
- Named: über mkfifo() erzeugt, 
- Unnamed: nur von eng verwandet Prozessen nutzbar (z.b. von fork)

## Message Queues
- msgget()
- wenn voll dann angehalten gibt es nicht!
- Aufruf kommt zurück, sync. nicht vorhanden
- verwendet wenn Prozesse nicht ewarten wollen wenn Ressource nicht verfügbar

## Shared Memory
- schnellster aber unkomfortabelster
### Semaphore
- ist variable die mit 1 init. wird
- wenn belegt wird es dekrementiert
- wenn null, dann wird Prozess geblockt
- wenn inkrementiert dann wird blockierter Prozess fortgesetzt und wieder dekrementiert

### Signale
- async. oder sync. Ereignisse, werden Prozessen mitgeteilt
- vergleichbar mit Interrupts
- wenn Prozess nicht bereit Prozessor rauszugeben (while 1)
- mit CTRL+C wird Interrupt geschickt bzw Signal

# L6
## Bibliotheken
- dienen dem Komfort

## Systemaufruf
- Folie gutes Beispiel

# L7
## Funktion der Festplatte
- nur in ganzen Blöcken kommunizieren
- auf ganzer Platte verteilt volle und leere Blöcke

## Dateien
- viele kleine, wenig große
- häufig gelesen, selten geschrieben
- Mehrfachnutzung : lesen können viele, modifizieren meistens nur einer

## Dateisysteme
- Bilden Daten und Verzeichnisse von Blockstruktur der Festplatte aber
- Metadaten auch auf Festplatte

## einfaches Dateisystem
- gute Folie

## FAT
- strukturierung in einer Tabelle
- Bootsektor, Res. Sektoren und FAT haben fixe längen
- Wurzelverz. variabel
- Name ist Verzeichnis, darin sind Einträge fixer länge 
- vom ersten Block zum letzten 0xffff

## Ext2
- in Zylindergruppen unterteilt
- wachsen von innen nach außen
- mehrere Zylinder bilden gruppe (auf Festplatte)
- Zentrales Element ist Inode (Index node)
### Inode
- beschreibung der Dateien
- memory mapping relativ einfach
- man muss nicht block lesen um folge-block zu lesen

## Extents

## Konsistenzprüfung

## Fehlertolerante Dateisysteme

# L8
## 

# L9
## 










































