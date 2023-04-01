# EPINetz-Policy-Parser

## Workflow

Der Policy Parser besitzt zwei Hauptbestandteile: Regelmäßige, automatisierte Updates der Termlisten (im live-Betrieb via Cronjobs) und die Klassifizierung neuer Dokumente in Echtzeit. Während die Updates der Termlisten vergleichsweise ressourcenintensiv sind, ist das Berechnen der Policy Scores in den Dokumenten wenig aufwändig, da lediglich diese lediglich in ihre Tokens zerlegt,* diese Tokens mit den Termlisten abgeglichen und darauf basierend die Gesamtscore berechnet wird.

!! Offene Frage: Können Dokumente direkt in Tokens zerlegt aus der NLP-Pipeline bezogen werden, oder muss die Tokenization in dieser Pipeline geschehen? 

### 1. Regelmäßige Updates der Termlisten

Wöchentliche Erstellung der Termlisten. Seed-Terme der Seed-Accounts aus dem letzten Jahr. Ausgehend von Seed-Termen Random Walks über alle Tweets der vorangegangenen drei Monaten, um erweiterte Termlisten zu erhalten.

#### Seed-Term-Extraktion

!! Offene Frage: Ist händische Evaluation in der Extraktion der Seed Terme nötig? 

1. Read-in der Listen an Ausschüssen, Ministerien und Ausschussmitgliedern und der Twitter User IDs
2. Data Read-in von der EPINetz-Datenbank I: Tweets der Seed-Accounts (Ministerien, Ausschüsse & Ausschussmitglieder) von Datum X - 12 Monate
3. (Tokenization)
4. Berechnung des Chi^2-Werts für Terme
5. Filterung der Terme nach Chi^2, Werte variabel (default: 250 für Ausschussmitglieder, 500 für Ministerien, 30 für Ausschüsse)
6. Ergebnis: Liste an Seed-Termen pro Policy Feld als .csv


#### Erweiterte Termlisten

1. Data Read-in von der EPINetz-Datenbank II: alle EPINetz-Tweets von Datum X - 3 Monate (Wichtig: Query auf EPINetz Accounts beschränken)
2. (Tokenization)
3. Read-in der Seed Terme
4. Bilden des PMI-gewichteten Netzwerks aus den Noun-Words der Tweets
5. RWR für Seed Terme auf PMI Netzwerk
6. Normalisierung der Scores
7. Ergebnis: Erweiterte Termlisten pro Policy Feld als .csv

### 2. (live) Klassifikation neuer Dokumente

!! Offene Frage: Wie werden die Daten angeliefert - Ping der EPINetz-Datenbank bei neuen Dokumenten oder täglicher Download aller neuen Dokumente? 

!! Offene Frage: Sollen die Scores über alle Policy Felder normalisiert werden (Menge = 1)? 

1. Read-in der Dokumente
2. (Tokenization)
3. Read-in der erweiterten Termlisten
4. Klassifizierung der Dokumente auf Grundlage der in ihnen vorkommenden Terme und mit ihnen assoziierter Policy Felder
5. (Normalisierung der Scores über alle Policy Felder - Wahrscheinlichkeit eines Dokuments, zu einem Policy Feld zu gehören)
6. Ergebnis: 1 Vektor pro Dokument mit 1 Wert pro Policy Feld 
