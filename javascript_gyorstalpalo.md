JavaScript gyorstalpaló
===

A JavaScript Douglas Crockford szerint a világ legjobban félreértett programnyelve. Bár nagy, komoly alkalmazásokat írunk benne, mégsem vesszük a fáradságot, hogy igazán megtanuljuk -- még mindig úgy használjuk, mint a 90-es években, amikor csak egyszerű effektek készítésére volt jó.

Pár hónapja úgy döntöttem, hogy végre tényleg elmélyedek a JavaScriptben. A tanultakat ebben a rövid útmutatóban foglalom össze. Mivel még kezdő vagyok, könnyen előfordulhatnak benne hibák; minden javítást és javaslatot szívesen fogadok.

Igyekszem sok példát mutatni; ezeket gyakran a böngésző JavaScript konzolján mutatom be:

    > <kifejezés>
      <visszatérési érték>

Típusok
---

A JavaScript dinamikus, gyengén típusos nyelv. A *dinamikusság* azt jelenti, hogy a változóknak nincs kötött típusa, csak az értékeknek, a *gyenge típusosság* pedig azt, hogy az értékeket az értelmező bizonyos jól definiált, ám néha meglepő szabályok szerint más típusúvá alakíthatja:

    > 1 + "3"
      "13"

Azt, hogy egy kifejezés értéke milyen típusú, a `typeof` operátorral állapíthatjuk meg, melynek visszatérési értéke egy karakterlánc:

    > typeof "1"
      "string"
    > typeof 1
      "number"
    > typeof true
      "boolean"
    > typeof function(n) { return n*n; }
      "function" 

Változót a `var` kulcsszóval deklarálhatunk:

    > var n
    > n // érték nélküli változóra hivatkozunk
      undefined
    > var a = 5 // a deklarációnál kezdőértéket is megadunk
    > a
      5

### Őstípusok ("primitív típusok")

#### Szám (`number`)

A JavaScriptben csak dupla pontosságú (64-bites) lebegőpontos számokat használhatunk. Ezek segítségével a 32-bites előjel nélküli egészek pontosan ábrázolhatók, törtszámok használata esetén viszont figyelnünk kell a jól ismert és méltán rettegett kerekítési hibákra:

    > 0.001 + 1.001
      1.0019999999999998

Literális számértékeket nyolcas, tízes és tizenhatos számrendszerben adhatunk meg:

    > 012
      10
    > 10
      10
    > 0xb
      10

A nyolcas számrendszerbeli literálok szintaxisa nem túl szerencsés. Vezető nullát tartalmazó szövegek számmá alakításakor például nagyon könnyű belefutni az alábbi problémába (régebbi JavaScript-verziókban):

    > parseInt("012")
      10
    > parseInt("012", 10)
      12

Emiatt érdemes minden esetben a második példa szerint eljárni, vagyis a `parseInt` második paramétereként megadni a számrendszer alapszámát.

Tízes számrendszerben kitevős alakban (a tíz valamely hatványának többszöröseként) is megadhatunk számokat:

    > 1e1
      10
    > 0.5e+2
      50
    > 0.5E-2
      0.005

Az IEEE 754-es szabvány három különleges értéket is definiál: a *nem szám* értéket, a pozitív és a negatív végtelent. JavaScriptben mindhármat megadhatjuk literális értékkel: `NaN`, `Infinity`, `-Infinity`. Ezeket a valós számok halmazán nem értelmezett, vagy a dupla pontosságú típus ábrázolási tartományából kivezető műveletek eredményeként kapjuk:

    > 0 / 0
      NaN
    > Infinity - Infinity
      NaN
    > 5 / 0
      Infinity
    > 1e308 // a double maximális értéke
      1e308
    > 1e309
      Infinity      

Egy csak számokon értelmezett művelet eredménye akkor is `NaN`, ha az egyik operandusa nem szám típusú:

    > 1 * "kilenc"
      NaN
    > (function() {}) / 3
      NaN

#### Karakterlánc (`string`)

Karakterláncokat aposztrófok vagy idézőjelek között adhatunk meg:

    "szia"
    'mi a helyzet?'
    'mégis mi lenne a "helyzet"?!'
    "nem t'om..."

Összefűzni a `+` operátorral tudunk. Ez sajnos komoly hibaforrás, mert a számok összeadás műveletét is ugyanígy jelöljük:

    > "egyik" + 'másik'
      "egyikmásik"
    > 1 + 2
      3
    > "1" + 2
      "12"

A leggyakrabban HTML-szövegmezők értékeit felhasználva találkozunk a fent bemutatott problémával. Ha a `+` egyik operandusa `string` típusú, az értelmező a másikat is megpróbálja szöveggé alakítani. Ha a számokon értelmezett összeadást szeretnénk használni, először alakítsuk a szövegeket számmá a `parseInt` vagy a `parseFloat` függvények használatával (ha nem akarunk pórul járni, a `parseInt`-nél a számrendszer alapszámát is adjuk meg második paraméterként). --------- a parseFloatnál nem?

A `*` és `/` operátorok a fentivel ellentétesen mindkét operandusukat számmá próbálják alakítani:

    > 3 * "3"
      9
    > "9" / 3
      3

A karakterláncokban használhatjuk a C-ből jól ismert escape-szekvenciákat:

    \\ \' \" \n \r \t \v \f \b

Illetve a `\u` szekvencia segítségével tetszőleges Unicode karaktert illeszthetünk a szövegbe. A `"\u263A"` például egy "mosolygó fej" karaktert eredményez.

#### Definiálatlan (`undefined`)

Egyetlen lehetséges értéke létezik, az `undefined`.

Ha egy nem deklarált változónevet próbálunk használni, hibaüzenetet kapunk. Ha a `typeof` operátorral lekérdezzük egy ilyen név típusát, az `"undefined"` karakterláncot kapjuk vissza.

    > a
      ReferenceError: a is not defined
    > typeof a
      "undefined"

Az olyan deklarált változóknak, melyeknek még nem adtunk explicit módon értéket, az alapértelmezett értéke `undefined`.

    > var a;
    > a
      undefined

A JavaScript-függvények hívásakor a paramétereknek nem kötelező értéket adni. A híváskor elhagyott paraméterek értéke szintén `undefined`.

    > var f = function(a, b) {
          if (a === undefined) return 0;
          if (b === undefined) return 1;
          return 2;
      }
    > f()
      0
    > f(42)
      1
    > f(42, "foo")
      2

#### Üres (`null`)

Egyetlen lehetséges értéke létezik, a `null`. Általában az érvényes érték hiányát jelezzük vele.

#### Logikai típus (`boolean`)

Két értéke lehetséges, a `true` és a `false`. A C nyelvben megszokott logikai operátorok érvényesek rá.

Logikai kontextusban (logikai operátor operandusaként és `if`, `while`, stb. utasítások feltételeként) az értelmező bármilyen típusú értéket logikai típusúvá alakít.

* Az üres karakterlánc,
* a `null` érték,
* az `undefined` érték,
* a 0 szám,
* a `NaN` szám és
* a `false` érték

mindig `false`-ra értékelődik ki, ezért ezeket szokás *hamiskás* (falsy) értékeknek is nevezni. Az összes többi érték logikai értéke `true` lesz (truthy értékek).

Ha a fentieket is figyelembe vesszük, a legtöbb logikai operátor a C-hez hasonlóan működik (precedencia, rövidre zárás). Az összehasonlító operátorok működéséről viszont érdemes néhány szót ejtenünk.

Az *egyenlőség operátor* automatikus típuskonverziót is végez:

    > 1 == 1
      true
    > 1 == 2
      false
    > 1 == "1"
      true

Az *azonosság operátor* csak azonos típus esetén ad vissza `true` értéket:

    > 1 === "1"
      false
    > 1 === 1
      true
    > 1 === 2
      false

A *nem egyenlő operátor* szintén végez típuskonverziót:

    > 1 != 1
      false
    > 1 != "1"
      false
    > 1 != 2
      true

A *nem azonos operátor* eltérő típus esetén az érték vizsgálata nélkül `false` értéket ad vissza:

    > 1 !== 1
      false
    > 1 !== "1"
      true
    > 1 !== 2
      false

A `NaN` semmivel sem egyenlő, még önmagával sem, így egy érték NaN-ságának megállapítására az `isNaN` függvényt használhatjuk:

    > NaN == NaN
      false
    > isNaN(NaN)
      true

### Összetett típusok

#### Tömbök

Tömböt elemeinek felsorolásával hozhatunk létre:

    > var array = [4, 3, 2, 1]
    > array
      [4, 3, 2, 1]
    > var emptyArray = []
    > emptyArray
      []

Egy tömb többféle típusú elemet is tartalmazhat:

    [1, false, "hello", function(n) { return n*n; }, [1, 2]]

A fenti tömb például egy számot, egy logikai értéket, egy karakterláncot, egy függvényt, és egy másik, kételemű tömböt tartalmaz.

A tömb elemeit indexük segítségével érhetjük el:

    > array[0]
      4
    > var i = 1
    > array[i]
      3
    > array[0] = 5
    > array
      [5, 3, 2, 1]

A tömbök elemeit a `delete` operátorral törölhetjük. Ha nem az utolsó elemet töröljük, "lyukas" tömb keletkezik, amelyből egyes indexek hiányoznak.

    > var array = [2, 4, 6, 8]
    > delete array[1]
    > array[0]
      2
    > array[1]
      undefined
    > array[2]
      6
    > array
      [2, undefined, 6, 8]

Ha értékadásnál nem létező indexet használunk, az adott indexű elem létrejön. Így is keletkezhet "lyukas" tömb.

    > var array = [2, 4, 6, 8]
    > array[4] = 10
    > array
      [2, 4, 6, 8, 10]
    > array[7] = 12
    > array
      [2, 4, 6, 8, 10, undefined, undefined, 12]

Az indexelő `[]` operátor argumentuma bármilyen szám típusú kifejezés lehet. Ez egyben azt is jelenti, hogy akár törtszámok is lehetnek indexek:

    > array[0.5] = 4
    > array[0.5]
      4
    > array
      [5, 3, 2, 1]

Amint fent látható, még a Chrome JavaScript konzolját sem készítették fel törtindexeket tartalmazzó tömbök kiírására. Így hát minden kedves olvasót arra kérnék, tartózkodjon e bizarr és meglepő funkció használatától.

A tömböknek számos tulajdonsága és metódusa van, ezek közül csak néhányat említenék.

A tömb hosszát a `length` tulajdonság adja meg. Ez minden esetben a legnagyobb egész indexnél eggyel nagyobb szám, ahogy az alábbi, szándékosan kifacsart példa mutatja:

    > var t = [1, 2]
    > t.length
      2
    > t[4] = 5
    > t
      [1, 2, undefined, undefined, 5]
    > t.length
      5
    > t[0.5] = 1.5
    > t.length
    5
    > t[4.5] = 5.5
    > t.length
    5

Fontos még a `push` és `pop` függvény, ezek segítségével veremként használhatjuk a tömböt.

    > var stack = []
    > stack.push(1)
    > stack.push(2)
    > stack.push(3)
    > stack.pop()
    > 3
    > stack
      [1, 2]

#### Függvények
#### Objektumok

Meglepetések
---

(pl. pontosvessző-beillesztés, parseInt, objektum- és tömbliterálok utolsó eleme utáni vessző)