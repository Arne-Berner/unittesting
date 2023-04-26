
## Designpatterns

### CQS Command Query Separation
Command Query Separation (CQS): Commands sind Methoden mit Nebeneffekten, die void zurück geben. Queries sind das Gegenstück, sie geben einen Value zurück und sind Nebeneffektfrei. CQS sagt, dass diese beiden Methoden getrennt bleiben sollten. Dadurch werden die Methoden leserlicher und sie halten sich eher an Separation of Concerns pattern und Single Responsibility Pattern. Commands werden Mocks und Queries werden Stubs.

### Humble Object Pattern
Im Humble Object Pattern geht es darum Kontrollstrukturen (die mit Collaboratorn arbeiten) von den domainspezifischen Berechnungen zu trennen. So ist MVC z.B. ein Humble Object, aber auch wenn man nach der Hex Struktur geht, entstehen Humble Objects. Sie bestehen meist aus einem Controller und einem Part, der komplexe Berechnungen anstellt/wichtig für die Domain ist.

### Unit of Work Pattern/transaction
Das Transaction Pattern ist eine einfachere Version des Unit of Work Pattern, in der alle Aktionen zu einer Datenbank gesammelt werden (also user raussuchen, updaten, speichern) und erst wenn alle gesammelt wurden, nacheinander ausgeführt werden. Unit of Work sammelt auch alle Aktionen und führt sie alle zusammen aus. Sollte eine Aktion nicht ausführbar sein, gibt es einen Rollback und nicht wird ausgeführt. (Entity framework hat sein eigenes Unit of Work) 

### Repository Pattern
Im Repository Pattern geht es darum, alle Funktionalitäten, die auf die Datenbank zugreifen in ein Repository zu stecken. Nicht nur sind sie zentralisiert, sondern man kann auch direkt die Daten direkt in welche umwandeln, die vom Domain Layer genutzt werden.

### Execute/canExecute Pattern

### Failfast Pattern

### Object Mother/Test Data Builder
Das Object Mother Pattern stellt einfach Testdaten in einer simplen Methode mit default Werten für tests bereit. Also etwas in der Richtung  
```c#
private User CreateUser(
    string email = 'user@mycorp.com',
    UserType type = 'UserType.Employee',
    bool isEmailConfirmed = false)
{
    /* hier wird der User created und mit der Datenbank verbunden */
}
```  
Das Test Data Builder Pattern macht es ähnlich, nur in einer fluent expression weise.