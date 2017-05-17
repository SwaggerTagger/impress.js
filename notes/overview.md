## Overview
Kurzer Übeblick über die Architektur und die verwendeten Technologien, auf die später im Detail eingegangen wird

Frontend 
- Vue
- wird geservt über nginx

von hier aus über nginx als Reverse-Proxy an das 
Backend
- Play als Framework verwendet

Backend
1. skaliert und konvertiert das Bild
2. legt Meta-Informationen über das Bild in der Postgres-Datenbank ab
3. lädt das Bild zu Azure Blob-Storage hoch
4. schreibt einen Auftrag zum Taggen des Bildes in die Kafka-Event-Queue
5. gibt dem Frontend eine Ok-Meldung, dass das Bild erfolgreich in die Warteschlange aufgenommen wurde

Einer der Arbeitsprozesse, die für das Tagging zuständig sind, nimmt den Auftrag aus der Event-Queue, laden sich das Bild aus dem Blob-Storage herunter und taggen es. Hierfür wird Darknet ein neuronales Netzwerk verwendet. 
Wenn das Taggen beendet ist, wird das Ergebnis in zurück nach Kafka geschrieben

Im Play-Server werden diese Ergebnisse ausgelesen, in die Postgres-Datenbank geschrieben und mithilfe von Server Sent Events dem Frontend mitgeteilt, dass ein Bild erfolgreich getaggt wurde.

Für das Starten und Verwalten der einzelnen Container verwenden wir Kubernetes