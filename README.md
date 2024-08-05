# Modulprüfung Programmierung (alter Name: Softwaretechnologie)

Studiengänge: B.A. Informationsverarbeitung, B.A. Medieninformatik


## Beschreibung der Anwendung

Bei dem Programm **CorpEx** handelt es sich um einen textbasierten Korpus-Explorer. Es dient dazu, größere Korpora, die in verschiedenen Dateiformaten vorliegen können, effizient durchsuchen zu können. Als Ergebnis der Suche werden sog. *keyword in context*-Listen (KWIC) angezeigt, die das Suchwort sowie links und rechts davon den Kontext der Fundstelle anzeigen.

Startet man das Programm, kann auf der Kommandozeile ein Befehl eingegeben werden, der je nachdem auch Parameter entgegennimmt. 

Mit dem Befehl `import` kann ein Korpus importiert werden. Der Befehl erwartet ein Argument, und zwar einen Verzeichnisnamen. Alle Dateien innerhalb des Verzeichnisses, die auf `.xml` enden, werden dann eingelesen. Optional kann ein zweites Argument angegeben werden, nämlich ein Name für das Korpus. Wird kein Name angegeben, verwendet das Programm den Verzeichnisnamen.

Mit dem Befehl `load` kann ein Korpus geladen werden. Ist ein Korpus geladen, wird der Korpusname vor dem Prompt angezeigt. 

Der Befehl `search` erlaubt eine token-basierte Suche. D.h. es werden ausschließlich ganze Tokens gesucht, dieses aber sehr schnell. Als Ausgabe erscheint dann die KWIC-Liste.

## Aufgaben

Die Aufgaben bestehen darin, zu den drei Interfaces `IFileImporter`, `ICorpus` und `ITokenizer` eine Implementierung bereitzustellen. Es wird empfohlen, die drei Aufgaben in der angegebenen Reihenfolge zu bearbeiten.

Zusätzlich zur Implementierung der Interfaces müssen in der Klasse `CorpEx` die drei Methoden `getFileImporter()`, `getTokenizer()` und `getCorpus(String)` implementiert werden, so dass sie ein Objekt der von Ihnen entwickelten Klasse zurückgeben. Beachten Sie dabei dass `getCorpus(String)` einen Korpus-Namen als Argument erwartet, und dass das zurückgegebene Objekt auch den entsprechenden Namen tragen sollte.


### 1. Import von XML-Dateien

Das Interface `IFileImporter` definiert nur die Methode `importFile(File)`. Innerhalb der Methode muss: 

- geprüft werden, ob die Datei existiert und sie lesbar ist, 
- validiert werden, ob es sich um eine XML-Datei handelt,
- der Textinhalt extrahiert und ein `CorpusDocument`-Objekt angelegt werden.

Es ist extrem hilfreich, dazu existierenden Code zu nutzen. Diesen zu suchen, sich einzulesen, und ggf. dann zu importieren ist Teil Ihrer Aufgabe. Neben den XML-Lese-Funktion in der Standardbibliothek gibt es auch einige frei verfügbare Bibliotheken.

Beachten Sie, 

- dass die Dokumente nur auf wohlgeformtheit überprüft werden müssen. Das Validieren gegen ein XML-Schema machen wir nicht, da uns die Details der XML-Struktur ja ohnehin nicht interessiert. 
- dass der Textinhalt sich nicht nur auf die druckbaren Zeichen bezieht, sondern sämtliche Zeichen beinhaltet, die nicht Teil eines XML-Tags sind. Das beinhaltet auch Zeilenumbrüche, Leerzeichen und so weiter.
- dass der Import für *beliebige* XML-Dateien funktionieren soll. Sie sollten sich also nicht zu sehr an den Beispielen orientieren.

### 2. Tokenisierung des Textinhalts

Das Interface `ITokenizer` definiert ebenfalls eine Methode, die wenig überraschend `tokenize` heißt, und ein String als Argument bekommt. In CorpEx wird hier der Textinhalt des CorpusDocument übergeben.

Die Tokenisierungsregeln sind in der Klassendokumentation zum Interface festgehalten. Wichtig ist hier vor allem, sich klar zu machen, dass ein Token hier kein String ist, sondern im Wesentlichen ein paar aus Integer-Zahlen, die Anfang und Ende des Tokens im Gesamtstring markieren. Der Tokenizer muss Leerzeichen etc. also mitzählen, damit die Character-Positionen stimmen. Um den tatsächlichen Zeicheninhalt des Tokens zu ermitteln, wird die Methode `substring(begin, end)` aus der Klasse `String` aufgerufen (siehe zur Verdeutlichung auch die Klassen `Token` und `TestToken`).

Die Token-Klasse verifiziert bei der Initialisierung, dass es sich um ein "legales Token" handelt. Illegale Tokens sind etwa, wenn eine der Positionsangaben < 0 ist oder wenn beide Positionsangaben gleich sind. Auch muss das Ende des Tokens immer größer sein als der Anfang.

### 3. Korpus-Suche

Das letzte zu implementierende Interface ist `ICorpus`. Darin werden mehrere Methoden definiert. `getName()` liefert einfach nur einen Namen für das Korpus zurück, der vorher festgelegt wurde. Der Kern der dritten Aufgabe liegt in den beiden Methoden `add(CorpusDocument)` und `search(String, boolean)`.

Damit die Suche effizient abläuft, indiziert das Korpus-Objekt jedes hereinkommende Dokument. Dazu wird es tokenisiert (mit dem o.g. Tokenizer), und die einzelnen Tokens werden in einem sog. invertierten Index gespeichert. Bei der Suche wird auf den Index zugegriffen, so dass nicht mehr über alle Dokumente iteriert werden muss.


### 4. Optionale Aufgaben

#### 4.1. Groß- und Kleinschreibung bei der Suche

Die Methode `search` empfängt auch ein boolean-Argument `ci`, das kontrolliert, ob die Suche unter Berücksichtigung von Groß- und Kleinschreibung (cased oder case-sensitive) funktioniert oder eben nicht (uncased oder case-insensitive). Implementieren Sie diese Möglichkeit, so dass Benutzer an ihre Sucheingabe den Modifikator `ci` anhängen können, um eine uncased-Suche zu bekommen.

Wenn Sie sich für die Aufgabe entscheiden, ändern Sie in der Klasse `TestCorpus` in den Rückgabewert der Methode `optionalTask()` auf `true`.

#### 4.2. Konfigurierbare Tokenisierung

In der Basisimplementierung des Tokenizers ist festgeschrieben, welche Zeichen als Grenzen zwischen Tokens verwendet werden. Die Erweiterung besteht hier darin, das Interface `IExtendedTokenizer` zu implementieren. Darin wird eine zusätzliche Methode definiert, die es uns erlaubt, die Zeichen anzugeben, an denen tokenisiert wird. 

Wenn Sie sich für die Aufgabe entscheiden, ändern Sie in der Klasse `TestTokenizer` in den Rückgabewert der Methode `optionalTask()` auf `true`, um Ihre Implementierung zu testen. Achten Sie außerdem darauf, dass die Methode `getTokenizer()` in der Klasse `CorpEx` dann auch das richtige Objekt zurückliefert.


## Prüfungsregularien und Bewertung

Um die Prüfung zu bestehen, **muss** Ihr Programm alle gegebenen Unit-Tests bestehen (die natürlich -- abgesehen von den optionalen Aufgaben -- auch nicht verändert werden dürfen). Sie können die Unit-Tests beliebig oft bei sich selbst laufen lassen.

Darüberhinaus werden die folgenden Kriterien bei der Bewertung betrachtet:

- Korrektheit der Ergebnisse (auch bei allgemeineren Fällen als getestet)
- Dokumentation und Kommentierung
- Lesbarkeit und Gliederung des Codes
- Effizienz des Codes
- Erfüllung der optionalen Aufgaben

Bitte geben Sie Ihr Programm als zip-Datei ab, indem Sie es in Ilias hochladen.

## Hinweise

### Tokenizer

Da wir in unserem Falle an Character-Positionen interessiert sind, helfen uns die von der Java-Bibliothek bereitgestellten Klassen `StreamTokenizer` und `StringTokenizer` nicht weiter. Wie immer gibt es verschiedene Möglichkeiten, das Problem zu lösen, aber sei hier kurz skizziert:

Die [String](https://docs.oracle.com/javase/8/docs/api/index.html?java/lang/String.html)-Klasse stellt die Methode `indexOf()` bereit, die *im Prinzip* für uns nützlich sein kann. Sie gibt uns in einem String die (erste oder nächste) Position eines Zeichens an. Wenn wir nun eine Zählervariable verwenden, um über die einzelnen Positionen des Strings zu iterieren, können wir sozusagen nach vorne schauen, und herausfinden, wo jeweils das nächste Trennzeichen ist. Diese Zahl + 1 bildet dann den Anfang des nächsten Tokens.

### Suche

Ein invertierter Index enthält das gesamte Vokabular der indexierten Dokumente (also alle in ihnen vorkommenden verschiedenen Wörter je ein mal, auch Types genannt). Jedes Wort ist dabei an eine Liste (o.ä.) geknüpft, in der alle Dokumente aufgelistet sind, in denen das Wort vorkommt (also deren Namen, Pfad o.ä. - nicht die gesamten Dokumente!).

Invertiert wird dieser Index deshalb genannt, weil nicht die Dokumente die in ihnen enthaltenen Wörter listen, sondern die Wörter die Dokumente, in denen sie vorkommen. Eine solche Dokumentenliste bezeichnet man auch als Postings List.

Folgende Abbildung zeigt vereinfacht einen invertierten Index. Links finden sich die Wörter (Types), rechts in einer Liste die Dokumente (hier mit Nummern unterschieden), in denen die entsprechenden Wörter vorkommen:

![](index.png)


## Fragen

Zögern Sie nicht, sich bei Fragen oder Problemen an [mich](mailto:nils.reiter@uni-koeln.de) zu wenden. Dies bezieht sich insbesondere auch auf technische Probleme, die vielleicht mit Eclipse o.ä. bestehen.
