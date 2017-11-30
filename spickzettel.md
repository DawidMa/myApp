# L1
DMA wichtig für virtualisierung
## Interrupt
- Früher in Mikrocontrollern
- Ein laufendes Programm wird unterbrochen, anderer Code wird ausgeführt
- "Return from interrupt" kommt zurück zum pausierten Code
- Es gibt mehr als ein Interrupt auf einmal, in normalen BS nicht, nur in manchen MC (Microcontrollern)
- mit INTA wird gemerkt welcher 
# l2
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
## Systemstart
- 







