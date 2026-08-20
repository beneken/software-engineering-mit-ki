# Refactoring

Nein, nicht jeder Code-Smell muss im Code sofort behoben werden. 
Ein großes (altes) System kriegt ihr vermutlich nicht frei von technischen Schulden hin. Wenn dieser Code seit 10 Jahren so läuft, gibt es überhaupt keinen Handlungsbedarf. Jede Änderung müsstet ihr intensiv testen,
selbst wenn Unittests vorhanden sind, reichen die vermutlich alleine nicht aus um
alle Fehlerrisiken auszuschalten.

Für Refactoring gibt es gute Gründe
 1. Es gibt neue Anforderungen, die nicht mehr zum aktuellen Design passen
 2. Wir haben mit Code-Smells wirklich erkennbare Probleme. Das wird an Hotspots im Repository sichtbar, da sich da Fehlerberichte häufen (Issues anschauen!) oder dieselbe Datei von verschiedenen Teammitgliedern häufig geändert wird (Hotspots anschauen) 

 Hier gibt es einige Beispiele zur Analyse und zum Planen von Refactorings. 
