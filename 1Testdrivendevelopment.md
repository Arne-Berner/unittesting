## Wie Funktioniert gutes Testen?

### Was ist das Ziel vom Unit Testing?
Der Code soll durch Unit tests erweiterbar sein, ohne dass man sich Sorgen machen muss, etwas an anderer Stelle zu zerstören. Aber es soll auch für eine bessere Codestruktur sorgen und gegen bugs/regressions helfen.

### Wie viel kostet ein Test?
Das hängt von vier Faktoren ab:
* Muss ich den test refactorn, wenn ich den code refactor?
* Kann ich ihn nach jeder Codeänderung _schnell_ ausführen?
* Schlägt der Test falsch aus? (Falscher Alarm)
* Ist der Test einfach zu verstehen?  

Nicht jeder Test ist ein guter Test und einige Tests kosten mehr, als sie einbringen.

### Was ist ein Unittest?
Ein Unittest ist ein Stück Code, welches eine Funktionalität testet. Dies soll schnell geschehen und ohne das Produkt vom Test abhängig zu machen. Außerdem sollen im Test keine Abhängigkeiten nach außen (classic) oder zu anderen Klassen (london) vorkommen.

### Was ist ein Integration Test?
Ein Integration Test testet mehrere Funktionalitäten oder hat Abhängigkeiten nach außen. End to End Tests sind einfach Integration Tests mit mehr Abhängigkeiten und Funktionalitäten. Wir beschränken uns auf Unittests.

### Tests sind Teil des Codes
Code sollte möglichst simpel (KISS) und effizient (YAGNI) gehalten werden. Das gilt auch für Tests. Nur Tests, die auch gebraucht werden, sind gute Tests.

### Arrange, Act and Assert
Jeder Test besteht aus drei Phasen: Arrange, Act und Assert.
__Arrange__ (given) ist der Teil, in dem alle, für den Test benötigten, Objekte und Daten aufgebaut werden.
__Act__ (when) ist der Teil, indem die Objekte und Daten an die zu testenden Funktionen übergeben wird.  
__Assert__ (then) ist der Teil, indem das zu erwartende Ergebnis mit dem Ergebnis aus Act verglichen wird.
Es ergibt Sinn mit Assert zu beginnen, wenn man einen (TDD) Test schreibt. Außerdem gibt es bei Unittests (im Gegensatz zu Integrationtests) nur jeweils einen Block für jedes A
Übrigens versuche ich Arrange, Act und Assert möglicht kurz zu halten. 
Ein zu langer Arrange block zeigt mir, dass meine Funktion zu viele Abhängigkeiten hat. 
Wenn der Act Abschnitt länger als eine Zeile ist, hat die Funktion nicht nur ?_eine_ Aufgabe, wie es Single Responsibility vorschreibt. Und wenn der Arrange Block zu lang ist, hatte die Funktion auch mehr als eine Aufgabe.


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
An jede Software werden Anforderungen gestellt. 
__Test Driven Development__ bedeutet, dass diese Anforderung in einem "Failing Test" übersetzt wird, bevor eine Funktion dazu entsteht. Dieser Test sollte immer erstmal fehlschlagen. Schließlich ist noch keine Funktion da.
Dann wird so lange an der Funktion geschrieben, bis der Test nicht mehr fehlschlägt. Abschließend wird refactored, der Code wird also überarbeitet, bis er einfach zu ändern und gut verständlich ist.
Diese drei Phasen: Test schlägt fehl - in vielen IDEs rot, der Test akzeptiert den Code - grün, und der Code wird überarbeitet - an Ampeln erinnernd oft als gelb bezeichnet, nutze ich für alle Anforderungen. Das sorgt für eine sehr solide Codestruktur und sinnvolle Tests.


### Die vier Säulen eines guten Unit tests
Tests sollten 
1. gegen regressionen (Fehler, die nach Veränderungen auftreten) schützen
2. ressistent gegens refactoring sein
3. schnelles Feedback geben
4. maintainbar sein

#### Die erste Säule: Schutz gegen Bugs
Tests sollen helfen vor Bugs zu schützen.
Je mehr Code da ist, desto schneller entstehen Bugs. 
Vorallem Bugs die ich schwieriger finden werde, weil das System komplexer wird. Um zu gucken, wie gut ein Test gegen Bugs schützt, schaue ich mir folgendes an: 
* die Menge des Codes, der während des Tests ausgeführt wird
* die Komplexität des Codes
* wie wichtig der Code für die Domain ist  

Je stärker ausgeprägt die ersten beiden Punkte sind, umso schneller entstehen Bugs und je stärker ausgeprägt der dritte Punkt ist, desto wichtiger ist es den Schutz zu erhöhen. Das heißt: Kurzer, unwichtiger Code für die Domain muss _nicht_ getestet werden.

#### Die zweite Säule: Ressistenz gegen Refactoring
Immer wenn sich die Anforderungen ändern, müssen sich die Tests ändern. Wenn ich aber einen Test schreibe, der sich immer ändert, wenn ich etwas am Code ändere, dann habe ich ein Problem. Sogenannte "false positives" können auftauchen.
Ein false Positive ist ein Test, der meint, es läuft was falsch, obwohl der Code funktioniert. 
Das kommt häufig zustande, weil sich Namen oder "was auf wen zugreift" verändert haben und der Test sich auf implementation Details bezieht.
Implementations Details beschreibt das "Wie?" ein Code zum Ziel kommt. Um die Ressistenz zu erhöhen sollte mein Test aber das "Was?" prüfen. Also muss sich jeder Test um das Verhalten oder die Anforderungen drehen. 

#### Zusammenhang zwischen der ersten und zweiten Säule
![protection against regression vs resistance to refactoring](./Diagrams/regressionvsrefactoring)
Beide zielen darauf ab, die genauigkeit von Tests zu erhöhen. Auf der einen indem Bugs verhindert werden und auf der anderen Seite, indem false positives nicht entstehen.

#### Die dritte Säule: Schnelles Feedback
Wenn die Tests zu lange laufen, werden sie nichtmehr ausgeführt. Das kann passieren, wenn sie, wie Integrationtests, Zugriffe auf Abhängigkeiten haben oder mehrere Verhaltensweisen auf einmal testen.

#### Die vierte Säule: Maintainbarkeit
Wenn Tests zu komplex und schwer verständlich werden, weil sie nicht refactored werden oder wenn Code oder Tests starke Abhängigkeiten haben, die immer mit bearbeitet werden müssen, leidet die Maintainbarkeit.

#### Das Zusammespiel der vier Säulen
Wie gut ein Test ist, lässt sich durch diese Formel schätzen.  
$\text{Erste Säule} \cdot \text{zweite Säule} \cdot \text{dritte Säule} \cdot \text{vierte Säule} = \text{Wie gut der Test ist}$  
Dabei gehen die Werte für jede Säule von 0 (nicht vorhanden) bis 1 (perfekt vorhanden). Das heißt, dass eine einzige 0 zu einem Gesamtergebnis von 0 führt!
Das Problem ist, die ersten drei Säulen widersprechen sich. 
Die erste Säule bedeutet, dass möglichst viel getestet wird, die zweite Säule, dass nur das wichtigste Verhalten getestet wird und die dritte Säule, dass möglichst schnell getestet wird. 
Nur die vierte Säule führt zu keinem widerspruch und kann immer eine 1 erreichen.  
Beispiele für Tests die nur zwei Säulen erfüllen: 
* End-To-End-Tests: Sie haben die erste und zweite Säule, aber nicht die dritte (sie testen alles, aber sind langsam)
* Mockeries/Triviale Tests: Sie haben die zweite und dritte, nicht aber die erste (sie sind schnell, sind unabhängig vom Code, finden aber keine echten Fehler)
* "Brittle" Tests: Sie haben die erste und dritte, nicht aber die zweite (sie sind schnell und testen wichtige Dinge, funktionieren aber nach Veränderungen des Codes nicht mehr)  

![drei Säulen](./Diagrams/dreisaeulen)
Jetzt könnte man denken, alle drei sollten gleich behandelt werden, aber in Wahrheit ist die zweite Säule entweder vorhanden, oder eben nicht. 
Die Tests testen nämlich entweder das "Was?"(1) oder das "Wie?"(0). Die echte Wahl besteht also zwischen der ersten und dritten Säule.  
![dreisäulenwastun](./Diagrams/3saeulenwastun)

### Sichtbares Verhalten (Was?)
Sichtbares Verhalten muss eines von zwei Dingen tun:
* Eine Operation verfügbar machen, die das Ziel der Benutzenden erreicht. 
Eine Operation ist eine Methode welche eine Berechnung durchführt, einen Seiteneffekt hat oder beides.
* Einen *State* verfügbar machen, der hilft, das Ziel der Benutzenden zu erreichen. Der *State* ist der derzeitige Zustand des Systems  
Die Benutzenden sind in dem Fall jede Person, die deine Schnittstelle benutzt.
Also auch wir Developer sind Benutzende, wenn wir eine Schnittstelle im Code nutzen.

Oftmals ist die öffentliche API eines Programms auch das Verhalten. Im folgenden Code hat sich ein Fehler eingeschlichen. Ein Implementationsdetail ist Teil der öffentlichen API. 
Die Funktion Normalizename ist muss für die nutzenden nicht Erreichbar sein. 
Die Nutzenden möchten nur den Namen ändern. 
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
Um nur das sichtbare Verhalten öffentlich zu machen, muss das normalizename privat sein und teil des Setters werden. 
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

Die Methode NormalizeNames sollte nur im Setter geprüft werden, nicht eigenständig. Es geht auch nicht anders, sonst müsste sie public sein.
Wenn ich den Test geschrieben hätte, bevor ich den Code geschrieben habe, hätte ich den Fehler nicht gemacht.
Als weiteres Beispiel:  

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

Der Nutzer möchte nur die Render funktion nutzen, von den Subrenderern muss er nichts wissen. Warum ist SubRenderers public?
Als Daumenregel kann man sagen: Wenn ich mehr als eine Operation auf einer API aufrufen muss, um ein einzelnes Ziel zu erreichnen, werden Implementationsdetails nach außen getragen.

### Sichtbares Verhalten (retake)
![communicationandbehaviour](./Diagrams/communicationandbehaviour)

Für die außenstehenden Benutzenden ist der Call vom Controller zum User kein sichtbares Verhalten, sie möchten nur, dass die Email sich ändert. Wenn wir aber eine Stufe tiefer gehen, benutzt der Controller User und somit wird das Verhalten vom User zum sichtbaren Verhalten für den Controller. Das sichtbare Verhalten verändert sich also je nach perspektive und muss entsprechend getestet werden.