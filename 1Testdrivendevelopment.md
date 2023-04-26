## Wie Funktioniert gutes Testen?

### Was ist das Ziel vom Unit Testing?
Der Code soll durch Unit tests erweiterbar sein, ohne dass man sich Sorgen machen muss, etwas an anderer Stelle zu zerstören. Aber es soll auch für eine bessere Codestruktur sorgen und gegen bugs/regressions helfen.

### Wie viel kostet ein Test?
Das hängt von vier Faktoren ab:
* Muss ich den test refactorn, wenn ich den code refactor?
* Kann ich ihn nach jeder Codeänderung ausführen?
* Schlägt der Test falsch aus? (Falscher Alarm)
* Ist der Test einfach zu verstehen?  

Nicht jeder Test ist ein guter Test und einige Tests kosten mehr, als sie einbringen.

### Was ist ein Unittest?
Ein Unittest ist ein Stück Code, welches eine Funktionalität testet. Dies soll schnell geschehen und ohne das Produkt vom Test abhängig zu machen. Außerdem sollen im Test keine Abhängigkeiten nach außen (classic) oder zu anderen Klassen (london) vorkommen.

### Was ist ein Integration Test?
Ein Integration Test testet mehrere Funktionalitäten oder hat Abhängigkeiten nach außen. End to End Tests sind einfach Integration Tests mit mehr Abhängigkeiten und Funktionalitäten. Wir beschränken uns auf Unittests.

### Tests sind Teil des Codes
Code sollte möglichst simpel (KISS) und effinzient (YAGNI) gehalten werden. Das gilt auch für Tests. Nur Tests, die auch gebraucht werden, sind gute Tests.

### Arrange, Act and Assert
__Arrange__ (given) ist der Teil, indem alle Daten aufgebaut werden, die für den Test benötigt werden.  
__Act__ (when) ist der Teil, indem die Daten an die zu testenden Funktionen übergeben wird.  
__Assert__ (then) ist der Teil, indem das Ergebnis aus Act mit dem erwarteten Ergebnis verglichen wird.  
Es ergibt Sinn mit Assert zu beginnen, wenn man einen (TDD) Test schreibt. Außerdem gibt es bei Unittests (im Gegensatz zu Integrationtests) nur jeweils einen Block für jedes A.


```c#
//Arrange
var store = new Store();
store.AddInventory(Product.Shampoo, 10);
var customer = new Customer();

//Act
bool success = customer.Purchase(store, Product.Shampoo, 5);

//Assert
Assert.True(success); //ist success true?
Assert.Equal(5, store.GetInventory(Product.Shampoo)); // wurden 5 Shampoos im store abgezogen?
```

### TDD
__Test Driven Development__ bedeutet, dass die Anforderung in einem "Failing Test" umgesetzt wird. Dann wird so lange Code geschrieben, bis der Test nicht mehr fehlschlägt und dann wird refactored. Dieser Prozess wird für alle Anforderungen wiederholt und sorgt für eine sehr solide Codestruktur und sinnvolle Tests.

### Fact, Theorie und Memberdata
Facts sind Tests, die alles was sie brauchen im Arrange nutzen. Theories können einer Variable mehrere Daten zuweisen und so sehr ähnliche Tests verbinden. Diese Daten werden in der Inlinedata angegeben. Das wird parameterized tests genannt. Das einzige Problem ist, dass in der Inlinedata keine Funktionen genutzt werden können, weil Attribute zur Compiletime ausgewertet werden. Aber dafür gibt es Memberdata, welches statische Methoden in der Klasse über "nameof()" aufruft.

### Die vier Säulen eines guten Unit tests
Tests sollten 
1. gegen bugs schützen
2. ressistent gegens refactoring sein
3. schnelles Feedback geben
4. maintainbar sein

#### Die erste Säule: Schutz gegen Bugs
Je mehr Code da ist, desto schneller entstehen Bugs. Um zu gucken, wie gut ein Test gegen Bugs schützen muss, sollte man sich folgendes anschauen: 
* die Menge des codes, der während des Tests ausgeführt wird
* die Komplexität des Codes
* wie wichtig der Code für die Domain ist
Je höher die ersten beiden Punkte, umso schneller entstehen Bugs und je höher der dritte Punkt desto wichtiger ist es den Schutz zu erhöhen. Das heißt kurzer, unwichtiger Code für die Domain muss nicht getestet werden.

#### Die zweite Säule: Ressistenz gegen Refactoring
Wenn die Tests sich mehr auf den Code beziehen, als auf das Verhalten, dann können "false Positives" auftauchen. Ein false Positive ist ein Test, der meint, es läuft was falsch, obwohl der Code funktioniert. Das kommt häufig zustande, weil sich Namen oder was auf wen zugreift verändert haben. Um die Ressistenz zu erhöhen, darf man "implementation details" nicht testen und nur Verhalten testen.

#### Zusammenhang zwischen der ersten und zweiten Säule
![protection against regression vs resistance to refactoring](./Diagrams/regressionvsrefactoring)
Beide zielen darauf ab, die genauigkeit von Tests zu erhöhen.

#### Die dritte Säule: Schnelles Feedback
Wenn die Tests zu lange laufen, werden sie nichtmehr ausgeführt. Das kann passieren, wenn sie wie Integrationtests zugriffe auf Abhängigkeiten haben oder mehrere Verhaltensweisen auf einmal testen.

#### Die vierte Säule: Maintainbarkeit
Wenn Tests zu komplex und schwer verständlich werden, weil sie nicht refactored werden oder wenn Tests starke Abhängigkeiten haben, die immer mit bearbeitet werden müssen, leidet die Maintainbarkeit.

#### Das Zusammespiel der vier Säulen
Wie gut ein Test ist, lässt sich durch diese Formel schätzen.  
$\text{Erste Säule} \cdot \text{zweite Säule} \cdot \text{dritte Säule} \cdot \text{vierte Säule} = \text{Wie gut der Test ist}$  
Dabei gehen die Werte für jede Säule von 0 (nicht vorhanden) bis 1 (perfekt vorhanden). Man sieht schnell, das eine 0 zu einem Gesamtergebnis von 0 führt.
Das Problem ist, die ersten drei Säulen schließen sich Gegenseitig zum Teil aus. Die erste Säule bedeutet, dass möglichst viel getestet wird, die zweite Säule, dass nur das wichtigste Verhalten getestet wird und die dritte Säule, dass möglichst wenig getestet wird. Nur die vierte Säule sollte immer vorhanden sein.  
Beispiele für Tests die nur zwei Säulen erfüllen: 
* End-To-End-Tests: Sie haben die erste und zweite Säule, aber nicht die dritte (sie testen alles, aber sind langsam)
* Mockeries/Triviale Tests: Sie haben die zweite und dritte, nicht aber die erste (sie sind schnell, testen aber nichts wichtiges)
* "Brittle" Tests: Sie haben die erste und dritte, nicht aber die zweite (sie sind schnell und testen wichtige Dinge, können aber nicht verändert werden)  

![drei Säulen](./Diagrams/dreisaeulen)
Jetzt könnte man denken, alle drei sollten gleich behandelt werden, aber in Wahrheit ist die zweite Säule entweder vorhanden, oder eben nicht. Die Tests testen nämlich entweder das Behaviour(1) oder den Code(0),das heißt die Wahl besteht zwischen der ersten und dritten Säule.  
![dreisäulenwastun](./Diagrams/3saeulenwastun)

### Observable Behaviour (Verhalten)
Observable Behaviour muss eines von zwei Dingen tun:
* Eine Operation verfügbar machen, die das Ziel des Kunden erreicht. Eine Operation ist eine Methode welche eine Berechnung durchführt oder einen Seiteneffekt hat oder beides.
* Einen *State* verfügbar machen, der hilft, das Ziel des Kunden zu erreichen. Der *State* ist der derzeitige Zustand des Systems  

Oftmals ist die öffentliche API eines Programms auch das Behaviour. In folgenden Code ist auch ein Implementationsdetail teil der öffentlichen API. Normalizename ist nicht das, was der Nutzer möchte, der Nutzer möchte nur den Namen ändern. Es wird der State des Namens über den Setter verfügbar gemacht. Normalizename ist dabei eine Operation.  
```csharp
public class User
{
    public string Name {get; set}
    public string NormalizeName(string name)
{
    string result = (name ?? "").Trim();
    if (result.Length > 50)
        return result.Substring(0, 50);
    return result;
}
}
public class UserController
{
    public void RenameUser(int userId, string newName)
    {
        User user = GetUserFromDatabase(userId);
        string normalizedName = user.NormalizeName(newName);
        user.Name = normalizedName;
        SaveUserToDatabase(user);
    }
}
```
Um nur das Observable Behaviour public zu machen, muss das normalizename private sein und teil des Setters werden. 
```csharp
public class User
{
    private string _name;
    public string Name
        {
        get => _name;
        set => _name = NormalizeName(value);
    }
    private string NormalizeName(string name)
    {
        string result = (name ?? "").Trim();
        if (result.Length > 50)
            return result.Substring(0, 50);
        return result;
    }
}
public class UserController
{
    public void RenameUser(int userId, string newName)
    {
        User user = GetUserFromDatabase(userId);
        user.Name = newName;
        SaveUserToDatabase(user);
    }
}
```

Die Methode NormalizeNames sollte nur im Setter geprüft werden, nicht eigenständig. (Geht auch nicht anders, sonst müsste sie public sein)

```csharp
public class MessageRenderer : IRenderer
    {
    public IReadOnlyList<IRenderer> SubRenderers { get; }
    public MessageRenderer()
        {
            SubRenderers = new List<IRenderer>
            {
                new HeaderRenderer(),
                new BodyRenderer(),
                new FooterRenderer()
            };
        }
    public string Render(Message message)
    {
        return SubRenderers
            .Select(x => x.Render(message))
            .Aggregate("", (str1, str2) => str1 + str2);
    }
}
```

Der Nutzer möchte nur die Render funktion nutzen, von den Subrenderern muss er nichts wissen.
Als Daumenregel kann man sagen: Wenn man mehr als eine Operation auf einer API aufrufen muss, um ein einzelnes Ziel zu erreichnen, werden Implementationsdetails geleaked.

### Observable Behaviour (retake)
![communicationandbehaviour](./Diagrams/communicationandbehaviour)

Für den External Client ist der Call vom Controller zum User kein Observable behaviour, er möchte nur, dass die Email sich ändert. Wenn wir aber eine Stufe tiefer gehen, ist der Controller der Client für User und somit wird das Verhalten vom User zum Observable Behaviour.


### Fragen
können mehrere Singletons vom Testing framework erstellt werden? Separiert das Testing framework die einzelnen Singletons??
Was sind die designpatterns object mother und test data builder?
Wofür brauchen wir einen Viewservice und den anderen Service? Genauso sind die apiservices gefühlt unnötig?
Warum ist AreaService zum Beispiel public und nicht private? Nur für DI?

### notizen 
Warum haben wir überall Interfaces für die Klassen? wegen DI? warum brauchen wir DI für ganz normale kleine Hierarchien?
Wir könnten viele der Errorhandling tests zusammen fassen und in einer Theory abbilden?
Text fixture sind die Daten, die in den Test gegeben werden. Sie sollten für den Test immer gleichbleibend sein. (Zum Beispiel die SQL Errors)
Nutzen wir london school im System? Wir haben eigentlich keine Collaborator sondern alles wird gemocked.  
Unser System läuft über dependency injection. Das heißt überall werden Interfaces angesprochen und Zentral über den DI Countainer verwaltet.  
Config Dateien können im SUT nur eine Instanz haben, aber im Testsystem für jeden Test neu konfiguriert werden. Es ist nicht nötig, eine configdatein für alle Tests zu nutzen, im Gegensatz zu einer Datenbank.  
London school mockt dinge weg. Es wird im assert nicht gegen die tatsächliche klasse des collaborators geprüft, sondern nur die Interaktion zwischen dem SUT und dem collaborator. (oben ist klassich)
