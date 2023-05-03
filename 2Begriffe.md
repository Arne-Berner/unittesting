## Begrifflichkeiten

### SUT und Collaborator
Das System Under Test (SUT) ist der part des Codes, der getestet werden soll. 
In dem vorigen Beispiel wäre das Customer (Customer kommt auch in act vor). 
Der Collaborator stellt die nötigen Daten zur Verfügung. 
Oben wäre der Collaborator "Store" (ist im assert block, aber nicht im act block).

### London vs classical
London mocked alles was nicht zu einer Klasse gehört. 
Es besteht völlige Isolation innerhalb der Klasse.
Classical versucht eine Isolation zwischen Testsystem und Produktionscode herzustellen. 
Die Tests müssen nur untereinander unabhängig parallel laufen können.
![londonvsclassic](./Diagrams/londonclassic)

### Blackbox- und Whitebox-Testing
__Blackbox-Testing__ testet nur die Funktionalität, ohne auf die Implementation zu achten. Sie testet das "Was" und nicht das "Wie".  
__Whiteboxtesting__ testet die Struktur des codes und nicht die Funktionalität. Sie testet das "Wie" und nicht das "Was".  
Auch wenn Whitebox-Testing besser gegen Bugs schützt, weil es detaillierter ist, bietet es keine Möglichkeit zu refactorn. Dadurch ist Blackbox-Testing zu bevorzugen.

### Mocks, Stubs und Test-Doubles
__Test-Doubles__ sind alle Datentypen die nur für den Test erstellt werden, und quasi "nicht echt" sind. 
__Mocks__ (mock, spy) helfen rausgehende Daten zu prüfen. Sie werden oft im Arrange aufgebaut und im Assert auf Änderungen geprüft.
__Stubs__ (stub, dummy, fake) helfen hereinkommende Daten zu prüfen. Sie werden im Arrange aufgebaut und bieten Interaktionsmöglichkeiten für Act. Sie sollten nicht im Assert vorkommen.  
![mockstub](./Diagrams/mockstub)
Sollte ein Mock auch ein "Setup" haben, also eine Interaktionsmöglichkeit bieten, dann bleibt es ein Mock. 

```csharp
[Fact]
public void Purchase_fails_when_not_enough_inventory()
{
    var storeMock = new Mock<IStore>();
    storeMock
        .Setup(x => x.HasEnoughInventory(
        Product.Shampoo, 5))
        .Returns(false);
    var sut = new Customer();
    bool success = sut.Purchase(
        storeMock.Object, Product.Shampoo, 5);
    Assert.False(success);
    storeMock.Verify(
        x => x.RemoveInventory(Product.Shampoo, 5), Times.Never);
}
```

Wichtig: Im Setup steht eine andere Funktion als im Assert teil.

### Hexagonal Architecture
Die Hexagonal Architecture hat einen äußeren Application Layer, der mit allen anderen Dependencies wie GUI und Datenbanken spricht und einen inneren Domain Layer, in dem die Business Logik steckt. 
Das Observable Behaviour ist für den Application Layer die Anfragen die vom Externen Client (z.B. GUI/Datenbanken) kommen und im Domain Layer die einzelnen Unterziele, die vom Application Layer kommen. 

![hexagonal](./Diagrams/hexagonal)

### Styles of unit testing
Der __Output-Based-Style__ ist an der funktionalen Programmierung angelehnt. 
Das SUT bekommt einen Input und gibt ohne Nebeneffekte einen Output aus. 
Dieser Output wird gegen den erwarteten Output getestet. (Wie viel % Rabatt werden gegeben?)  
Der __State-Based-Style__ kommt in OOP viel vor, weil alle States die durch Nebeneffekte verändert werden, geprüft werden. (Ist das Produkt im Warenkorb Käse?)  
Der __Communication-Based-Style__ nutzt Mocks, um die Kommunikation zwischen dem SUT und den Collaborators zu testen. (Wurde der Befehl "sendEmail" an das EmailGatewayMock gesendet?)  
Alle drei Styles sind in der 1. und 3. Säule gleich stark. 
In der zweiten ist Output>State>Communication die Rangfolge, weil State oftmals mehr in seiner API exposed als Output. 
Communication style funktioniert nur dann, wenn eine Interaktion zu einem "Drittanwender" gegeben ist, ansonsten führt es zu starren Tests. 
Die vierte Säule hat die gleiche Rangliste wie die zweite Säule, einfach weil mehr Code entsteht.  

### functional programming
Um den Outputbased Style noch weiter zu treiben, kann man die Funktionalität der Anforderung in eine Klasse stecken und deren Seiteneffekte (also wo bekomme ich den Input her und was mache ich mit dem Output) an den Rand schieben. Das ganze wird dann vom Applicationlayer ummantelt und zusammen geführt. 

![144bild](./Diagrams/144bild)

Das ist nur nicht immer möglich und sollte auch nur für komplexere Anforderungen genutzt werden (mit vielen Entscheidungen z.b.).

### happy path und edge cases
Der __Happy Path__ ist, wenn alles so funktioniert, wie es funktionieren soll. Für einen Taschenrechner kann der Happy Path test sein, dass 1+1 die Zahl 2 sein soll. Der __Edge Case__ wäre hier, wenn jemand affe in den Taschenrechner eingibt. Das sollte einen Fehler werfen.

### Fact, Theorie und Memberdata
Facts, Theories und Memberdata sind xUnit eigene Konstrukte, die auch in anderen Testframeworks unter anderen Namen erscheinen.
Facts sind Tests, die alles was sie brauchen im Arrange nutzen.
Wobei Theories  einer Variable mehrere Daten zuweisen können. So werden sehr ähnliche Tests in einem Test vereint. 
Diese Daten kann ich in der so genannten Inlinedata - oft über dem eigentlichen Test stehend - angegeben. 
Das wird "parameterized" Tests genannt, weil mehrere Parameter für einen Test übergeben werden. 
Eine Funktion kann ich aber nicht als "Inlinedata" übergeben. Alle Attribute müssen bereits zur Kompilierzeit bekannt sein. Als abhilfe gegen diese Schwäche können statische Methoden mit nameof() über reflection aufgerufen werden. Die funktionen werden also nicht über ihre Funktionalität, sondern nur ihren Namen aufgerufen und der ist während der Kompilierzeit bekannt.