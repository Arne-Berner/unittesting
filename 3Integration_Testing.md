## Integration Testing
### Was sind Integration Tests?
__Integration Tests__ sind alle automatisierten Tests, die keine Unit Tests  oder end to end tests sind. 
Sie sind besonders ressistent gegen Refactoring, weil sie sich nur mit "unmanaged dependencies" (prozessen, die nicht zu der Anwendung gehören und über die man nicht bestimmen kann) beschäftigen. 
Das heißt sie prüfen nur die Communication die ganz am __Ende__ raus geht, indem man die Schnittstellen mockt. 
Dadurch sind sie zwar langsam, geben aber kaum false positives und schützen auch gut gegen regressions, außer der Drittanbieter ändert seinen Prozess.

### Mocks
Am besten mocked man nur unmangaged dependencies, weil die Kommunikation zwischen managed prozessen nicht wichtig ist und zu brittle tests führt. Außerdem sollten diese unmanaged dependencies wirklich nur am Rand sein.

### Base class
In der Base Class für die Tests sollten nur wichtige Funktionen drin stehen, wie eine Connection zum SQL server aufbauen, die überall benutzt wird. Meiner Meinung nach könnte man auch einfach eine "Test"-Utility-Klasse benutzen, wie in unserem System.

### Factory Methods
Damit Integration tests leichter zu maintainen sind, kann und sollte man factory methods für z.b. Interaktionen mit der Datenbank schreiben. Diese gehören erstmal nur in die jeweiligen Testklassen und nicht in die Baseclass.

### Datenbanken Testen
Man sollte immer eine echte (Test)-Datenbank nehmen. Sollte man keinen Zugriff auf die Datenbank haben, lohnt sich der Aufwand für Integrationtests kaum. Außerdem ist es wichtig, nur Statechanges zu testen oder sehr komplexe Vorgänge. Also sind normale "reads" (meistens) raus.

### Sollte man repositories testen?
Nein, eher nicht. Es ist sehr schwierig solche tests zu maintainen und sie schützen kaum vor regressions, weil normale integrationtests mit der Datenbank die gleichen regressions abdecken. Deswegen sollten repositories nur in einem Integrationtest mitgetestet werden, aber eher nicht das Ziel eines Tests sein.