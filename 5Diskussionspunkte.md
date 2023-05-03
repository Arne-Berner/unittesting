## Diskussionpunkte

### ?Probleme? der Classical School
Dadurch, dass Vehalten getestet wird, anstatt von Klassen, wird es schwierig sehr verzweigte und lange Dependencyschlangen zu testen, die ein Behaviour beinhalten. Aber das ist nur ein Fake-Problem, weil der Test darauf hinweist, das etwas im Code-Design falsch ist. Solche verzweigte Strukturen sollten nicht existieren.  
Ein weiteres Problem ist, dass ein Fehler zu vielen Failing Tests führen kann. Das ist aber oft durch beheben des einen Fehlers behoben und zeigt an, welcher Teil des Produkts wichtig ist. Außerdem ist es leicht behoben, wenn man häufig testet.

### Code coverage und branch coverage
Vorab: Weil Testcoverage kein guter Maßstab ist, nutzen wir es nicht.
Code Coverage schaut sich die Anzahl der Codezeilen an, die in einem Test vorkommen. Das Problem dabei ist, dass z.b. bei If Statement nur geschaut werden könnte, ob der Test falsch ist. Die würde bei einem if block nicht zu 100% coverage führen, bei einem ternären operator jedoch schon. Es ist also kein guter Indikator.  
Branch coverage geht ganze Zweige durch, die durch ifs oder switches entstehen. Das heißt wenn nur das false Statement geprüft wird im Test, hat man eine Branch coverage von 50%. Es ist der bessere Indikator. Aber auch hier können Nebeneffekte ignoriert werden, oder ein Test ohne "assert" könnte als coverage gelten. Auch externe libraries werden nicht getestet (soll auch gar nicht). Dabei lässt eine niedrige Coverage auf schlechten Code schließen, aber eine hohe % Zahl nicht auf guten Code. 

### Naming Conventions
Es ist sinnvoll Tests nach ihrem Verhalten zu benennen und nicht nach ihrem Vorkommen, Input und Output.
SumOfTwoNumbers ist leichter zu lesen als calculatorTakesTwoNumbersReturnsSumOfTwoNumbers.

### inter vs intrasystem interaction
Intersystem bedeutet, dass aus den Application Layer heraus eine Interaktion mit einem Drittanbieter stattfindet. Diese Drittanbieter sollten in Unittests immer gemocked werden, weil dann wirklich ein Observable behaviour getestet wird.  
Intrasystem bedeutet, dass innerhalb des Domain Layers Klassen miteinander kommunizieren. Die einzelnen Klassen müssen hier nicht gemockt werden, weil das dazu führen würde, dass man nur teilschritte testet, aber kein Observable behaviour. (Der Viewservice get ruft den Servicemock  get auf, wobei dieser aufruf nur ein Teilschritt vom tatsächlichen Behaviour ist, nämlich im Frontend etwas anzeigen zu lassen.) Intrasystem zu mocken ist die London Variante und führt zu Tests, die in der zweiten Säule schlecht abschneiden: Sie sind gegen refactoring nicht immun.