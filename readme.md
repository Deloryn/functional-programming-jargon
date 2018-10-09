# Słowniczek programowania funkcyjnego

Programowanie funkcyjne (FP) ma wiele zalet, w wyniku czego zyskuje popularność. Jednak każdy paradygmat programowania ma swój unikalny żargon i programowanie funkcyjne nie jest tutaj wyjątkiem. Poprzez dostarczenie Wam słowniczka, mamy nadzieję, że jego nauka będzie łatwiejsza.

Przykłady są przedstawione w JavaScript (ES2015). [Dlaczego JavaScript?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)


Tam, gdzie to możliwe, dokument ten korzysta z terminów zdefiniowanych w [Fantasy Land spec](https://github.com/fantasyland/fantasy-land)

__Tłumaczenia__
* [Link do oryginału (wersja angielska)](https://github.com/hemanth/functional-programming-jargon)
* [Portugalski](https://github.com/alexmoreno/jargoes-programacao-funcional)
* [Hiszpański](https://github.com/idcmardelplata/functional-programming-jargon/tree/master)
* [Chiński](https://github.com/shfshanyue/fp-jargon-zh)
* [Bahasa Indonesia](https://github.com/wisn/jargon-pemrograman-fungsional)
* [Scala World](https://github.com/ikhoon/functional-programming-jargon.scala)
* [Koreański](https://github.com/sphilee/functional-programming-jargon)
* [Polski](https://github.com/Deloryn/functional-programming-jargon)

__Spis treści__
<!-- RM(noparent,notop) -->

* [Arity](#arity)
* [Higher-Order Functions (HOF)](#higher-order-functions-hof)
* [Closure](#closure)
* [Partial Application](#partial-application)
* [Currying](#currying)
* [Auto Currying](#auto-currying)
* [Function Composition](#function-composition)
* [Continuation](#continuation)
* [Purity](#purity)
* [Side effects](#side-effects)
* [Idempotent](#idempotent)
* [Point-Free Style](#point-free-style)
* [Predicate](#predicate)
* [Contracts](#contracts)
* [Category](#category)
* [Value](#value)
* [Constant](#constant)
* [Functor](#functor)
* [Pointed Functor](#pointed-functor)
* [Lift](#lift)
* [Referential Transparency](#referential-transparency)
* [Equational Reasoning](#equational-reasoning)
* [Lambda](#lambda)
* [Lambda Calculus](#lambda-calculus)
* [Lazy evaluation](#lazy-evaluation)
* [Monoid](#monoid)
* [Monad](#monad)
* [Comonad](#comonad)
* [Applicative Functor](#applicative-functor)
* [Morphism](#morphism)
  * [Endomorphism](#endomorphism)
  * [Isomorphism](#isomorphism)
* [Setoid](#setoid)
* [Semigroup](#semigroup)
* [Foldable](#foldable)
* [Lens](#lens)
* [Type Signatures](#type-signatures)
* [Algebraic data type](#algebraic-data-type)
  * [Sum type](#sum-type)
  * [Product type](#product-type)
* [Option](#option)
* [Functional Programming Libraries in JavaScript](#functional-programming-libraries-in-javascript)


<!-- /RM -->

## Arity

Arność. Liczba argumentów pobieranych przez funkcję. Pochodzi od słów takich jak unary, binary, ternary itd. To słowo wyróżnia się tym, że składa się z dwóch przyrostków ("-ary" i "-ity"). Np. dodawanie pobiera dwa argumenty, zatem jest zdefiniowane jako funkcja binarna lub jako funkcja o arności równej 2. Ludzie, którzy preferują greckie korzenie nazewnictwa (zamiast łacińskich), mogą czasem taką funkcję nazywać "diadyczną". Kiedy ilość argumentów funkcji może być różna, jest ona nazywana funkcją ze zmienną liczbą argumentów (variadic). Natomiast funkcja binarna musi mieć dwa i tylko dwa argumenty. Zobacz poniżej: currying i partial application.

```js
const sum = (a, b) => a + b

const arity = sum.length
console.log(arity) // 2

// Arność funkcji sum wynosi 2
```

## Higher-Order Functions (HOF)

Funkcja, która pobiera funkcję jako argument i/lub zwraca funkcję.

```js
const filter = (predicate, xs) => xs.filter(predicate)
```

```js
const is = (type) => (x) => Object(x) instanceof type
```

```js
filter(is(Number), [0, '1', 2, null]) // [0, 2]
```

## Closure

Closure to trwały zakres zmiennych dostępnych dla danej funkcji w momencie, kiedy została utworzona. Jest to ważne dla
[partial application](#partial-application).


```js
const addTo = (x) => {
    return (y) => {
        return x + y
    }
}
```

Możemy wywołać `addTo`, podając pewną liczbę jako argument i otrzymamy funkcję z "wbudowaną" wartością`x`.

```js
var addToFive = addTo(5)
```

W tym przypadku `x` jest zawarte w closure `addToFive` z wartością 5. Możemy zatem wywołać `addToFive` z `y` i wtedy otrzymać pożądany wynik.

```
addToFive(3) // => 8
```

To działa, ponieważ zmienne, które są w zasięgu rodzica, nie są usuwane przez garbage-collectora tak długo, jak funkcja jest zachowana.

Closure są powszechnie wykorzystywane w event handlerach, dzięki czemu nadal mają dostęp do zmiennych zdefiniowanych u ich rodziców, kiedy są wywołane.

__Dalsza lektura__
* [Lambda Vs Closure](http://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)
* [Jak działają Closure w JavaScripcie?](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)


## Partial Application

Partial application polega na tym, że tworzymy nową funkcję, która z góry jest wypełniona niektórymi argumentami z funkcji bazowej.


```js
// Pomocnik do tworzenia funkcji korzystających z partial application
// Pobiera funkcję i część argumentów
const partial = (f, ...args) =>
  // zwraca funkcję, która pobiera resztę argumentów
  (...moreArgs) =>
    // i wywołuje oryginalną funkcję posiadającą je wszystkie
    f(...args, ...moreArgs)

// Przykładowa funkcja
const add3 = (a, b, c) => a + b + c

// Wykorzystujemy partial application do "wbudowania" liczb 2 i 3 do funkcji add3. Daje to nam jednoargumentową funkcję.
const fivePlus = partial(add3, 2, 3) // (c) => 2 + 3 + c

fivePlus(4) // 9
```

Możesz także użyć `Function.prototype.bind`, by wykonać partial application dla funkcji w JS:

```js
const add1More = add3.bind(null, 2, 3) // (c) => 2 + 3 + c
```

Partial application pomaga w tworzeniu prostszych funkcji poprzez "wbudowywanie" do nich danych dla funkcji bardziej złożonych. ["Curried functions"](#currying) automatycznie korzystają z partial application.


## Currying

Proces zamiany funkcji, która pobiera wiele argumentów, w funkcję, która pobiera je wszystkie na raz.

Za każdym razem, kiedy funkcja jest wywołana, przyjmuje tylko jeden argument i zwraca funkcję, która pobiera jeden argument. Proces trwa do momentu, aż wszystkie argumenty zostaną przekazane.

```js
const sum = (a, b) => a + b

const curriedSum = (a) => (b) => a + b

curriedSum(40)(2) // 42.

const add2 = curriedSum(2) // (b) => 2 + b

add2(10) // 12

```

## Auto Currying
Przekształcanie funkcji, która pobiera wiele argumentów, w jedną, która ma taką cechę: jeśli podamy mniej argumentów niż wynosi jej arność, to zwraca funkcję, która pobiera resztę potrzebnych argumentów. Kiedy funkcja dostaje odpowiednią liczbę argumentów, to wtedy obliczana jest jej wartość.

lodash & Ramda mają funkcję `curry`, która działa w ten sposób.

```js
const add = (x, y) => x + y

const curriedAdd = _.curry(add)
curriedAdd(1, 2) // 3
curriedAdd(1) // (y) => 1 + y
curriedAdd(1)(2) // 3
```

__Dalsza lektura__
* [Favoring Curry](http://fr.umio.us/favoring-curry/)
* [Hey Underscore, You're Doing It Wrong!](https://www.youtube.com/watch?v=m3svKOdZijA)

## Function Composition

Łączenie dwóch funkcji w trzecią funkcję tak, że wyjście (output) jednej funkcji jest wejściem (input) drugiej.

```js
const compose = (f, g) => (a) => f(g(a)) // Definicja
const floorAndToString = compose((val) => val.toString(), Math.floor) // Użycie
floorAndToString(121.212121) // '121'
```

## Continuation

W dowolnym miejscu w programie, ta część kodu, która pozostała jeszcze do wykonania, nazywana jest kontynuacją.

```js
const printAsString = (num) => console.log(`Wynik: ${num}`)

const addOneAndContinue = (num, cc) => {
  const result = num + 1
  cc(result)
}

addOneAndContinue(2, printAsString) // 'Wynik: 3'
```

Kontynuacje często się pojawiają w programowaniu asynchronicznym, kiedy program potrzebuje zaczekać na otrzymanie danych, zanim będzie mógł kontynuować. Odpowiedź często jest przekazywana do reszty programu, która jest kontynuacją po jej otrzymaniu.

```js
const continueProgramWith = (data) => {
  // Kontynuuję wykonywanie programu z tymi danymi
}

readFileAsync('path/to/file', (err, response) => {
  if (err) {
    // obsługa błędu
    return
  }
  continueProgramWith(response)
})
```

## Purity

Czystość. Funkcja jest "czysta" (pure) wtedy, kiedy wartość przez nią zwracana jest zdeterminowana jedynie przez jej wartości wejściowe i nie ma przy tym żadnych "efektów ubocznych" (side effects).

```js
const greet = (name) => `Hi, ${name}`

greet('Brianne') // 'Hi, Brianne'
```

W przeciwieństwie do poniższych przykładów:

```js
window.name = 'Brianne'

const greet = () => `Hi, ${window.name}`

greet() // "Hi, Brianne"
```

Wynik powyższego przykładu bazuje na danych przechowywanych poza funkcją...

```js
let greeting

const greet = (name) => {
  greeting = `Hi, ${name}`
}

greet('Brianne')
greeting // "Hi, Brianne"
```

... a w tym przykładzie modyfikowany jest stan na zewnątrz funkcji.

## Side effects

Funkcja lub wyrażenie ma "efekt uboczny" (side effect) wtedy, kiedy oprócz zwracania wartości, zachodzi interakcja (odczyt lub zapis) z zewnętrznym, mutowalnym stanem.

```js
const differentEveryTime = new Date()
```

```js
console.log('IO is a side effect!')
```

## Idempotent

Funkcja jest idempotentna wtedy, kiedy jej ponowne wywołanie na jej wartości (zobacz poniżej) nie zwraca innego wyniku.

```
f(f(x)) ≍ f(x)
```

```js
Math.abs(Math.abs(10))
```

```js
sort(sort(sort([2, 1])))
```

## Point-Free Style

Sposób pisania funkcji, w którym nie mają one jawnej definicji argumentów, z jakich korzystają. Ten styl zazwyczaj potrzebuje wykorzystania [currying](#currying) lub innych [funkcji wyższego rzędu](#higher-order-functions-hof). Styl znany również jako "Tacit programming".

```js
// Na początku mamy:
const map = (fn) => (list) => list.map(fn)
const add = (a) => (b) => a + b

// Później:

// Nie w stylu points-free - `numbers` to argument jawny
const incrementAll = (numbers) => map(add(1))(numbers)

// W stylu points-free - lista jest argumentem niejawnym
const incrementAll2 = map(add(1))
```

`incrementAll` identyfikuje i korzysta z parametru `numbers`, więc to nie jest w stylu points-free. Funkcja `incrementalAll2` jest napisana poprzez łączenie funkcji i wartości w taki sposób, że nie ma wzmianki o jej argumentach. __Jest__ to zatem points-free.

Definicja funkcji napisanej w stylu points-free wygląda tak, jak normalne przypisania bez `function` czy `=>`.

## Predicate
Predykat to funkcja, która zwraca albo prawdę (true), albo fałsz (false) dla podanej wartości. Powszechnym zastosowaniem predykatu jest callback dla array filter.

```js
const predicate = (a) => a > 2

;[1, 2, 3, 4].filter(predicate) // [3, 4]
```

## Contracts

Kontrakt określa obowiązki i gwarancje zachowania z funkcji lub wyrażenia w momencie wykonywania. Zachowuje się jak zestaw zasad, które są oczekiwane przez wejście i wyjście funkcji lub wyrażenia. Kiedykolwiek kontrakt zostanie naruszony, błędy są raportowane.

```js
// Zdefiniuj nasz kontrakt : int -> int
const contract = (input) => {
  if (typeof input === 'number') return true
  throw new Error('Contract violated: expected int -> int')
}

const addOne = (num) => contract(num) && num + 1

addOne(2) // 3
addOne('some string') // Kontrakt naruszony: oczekiwany jest int -> int
```

## Category

Kategoria (w teorii kategorii) jest kolekcją obiektów i morfizmów między nimi. W programowaniu, typy zwykle zachowują się jak obiekty, a funkcje jak morfizmy.

Aby kategoria była poprawna, muszą być spełnione 3 zasady:

1. Musi być morfizm tożsamości, który mapuje obiekt do siebie.
    Gdzie `a` jest obiektem w jakiejś kategorii,
    tam musi być funkcja postaci: `a -> a`.
2. Morfizmy muszą być "składalne".
    Gdzie `a`, `b` i `c` są obiektami jakiejś kategorii
    i `f` jest morfizmem postaci `a -> b` oraz `g` jest morfizmem postaci `b -> c`;
    `g(f(fx))` musi być równoważne `(g • f)(x)`.
3. Kompozycja musi być asocjacyjna.
    `f • (g • h)` jest tym samym, co `(f • g) • h`

Ponieważ te zasady rządzą kompozycją na bardzo abstrakcyjnym poziomie, teoria kategorii jest świetna w odkrywaniu nowych sposobów komponowania rzeczy.

__Dalsza lektura__

* [Teoria kategorii dla programistów](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)

## Value

Value (wartość) to cokolwiek, co może zostać przypisane do zmiennej.

```js
5
Object.freeze({name: 'John', age: 30}) // Funkcja `freeze` wymusza niemutowalność
;(a) => a
;[1]
undefined
```

## Constant

Stała (constant) to zmienna, do której nie można już nic przypisać, gdy raz została już zdefiniowana.

```js
const five = 5
const john = Object.freeze({name: 'John', age: 30})
```

Stałe cechuje [referential transparency](#referential-transparency. That is, they can be replaced with the values that they represent without affecting the result.

With the above two constants the following expression will always return `true`.

```js
john.age + five === ({name: 'John', age: 30}).age + (5)
```

## Functor

Obiekt, który implementuje funkcję `map`, która - działając na każdej wartości obiektu i tworząc nowy obiekt - trzyma się dwóch zasad:

__Zachowuje tożsamość__
```
object.map(x => x) ≍ object
```

__Jest kompozycyjna (zobacz niżej)__

```
object.map(compose(f, g)) ≍ object.map(g).map(f)
```

(`f`, `g` są dowolnymi funkcjami)

Powszechnym funktorem w JavaScripcie jest `Array`, ponieważ stosuje się do dwóch zasad funktora:

```js
;[1, 2, 3].map(x => x) // = [1, 2, 3]
```

oraz

```js
const f = x => x + 1
const g = x => x * 2

;[1, 2, 3].map(x => f(g(x))) // = [3, 5, 7]
;[1, 2, 3].map(g).map(f)     // = [3, 5, 7]
```

## Pointed Functor
Obiekt z funkcją `of`, która umieszcza w nim pojedynczą wartość _any_.

ES2015 udostępnia `Array.of`, który zmienia tablice w pointed functory.

```js
Array.of(1) // [1]
```

## Lift

Lifting jest wtedy, kiedy bierzesz wartość i wrzucasz ją do obiektum tak jak w [pointed functor](#pointed-functor). Jeśli liftingujesz funkcję do [Applicative Functor](#applicative-functor), możesz sprawić, że zadziała także na tych wartościach, które są w tym funktorze.

Aby ułatwić korzystanie z funkcji na funktorach, niektóre implementacje mają funkcje `lift` lub `liftA2`.

```js
const liftA2 = (f) => (a, b) => a.map(f).ap(b) // zauważ, że to jest `ap`, a nie `map`

const mult = a => b => a * b

const liftedMult = liftA2(mult) // ta funkcja działa teraz na funktorach tak, jak tablica

liftedMult([1, 2], [3]) // [3, 6]
liftA2(a => b => a + b)([1, 2], [3, 4]) // [4, 5, 5, 6]
```

Lifting to jednoargumentowa funkcja i używanie jej robi to samo, co `map`.

```js
const increment = (x) => x + 1

lift(increment)([2]) // [3]
;[2].map(increment) // [3]
```


## Referential Transparency

Referential transparency cechuje się tym, że wyrażenie może być podmienione na swoją wartość bez zmieniania zachowania programu.

Mamy funkcję `greet`:

```js
const greet = () => 'Hello World!'
```

Każde wywołanie `greet()` może być zamienione na `Hello World!`, zatem `greet` jest referencyjnie transparentne.

##  Equational Reasoning

Kiedy aplikacja jest złożona z wyrażeń i pozbawiona side effectów, prawdy o systemie można wyprowadzić z jego części.

## Lambda

Funkcja anonimowa, która może być traktowana jak wartość.

```js
;(function (a) {
  return a + 1
})

;(a) => a + 1
```
Lambdy są często przekazywane jako argumenty do [funkcji wyższego rzędu](#higher-order-functions-hof).

```js
;[1, 2].map((a) => a + 1) // [2, 3]
```

Możesz przypisać wyrażenie lambda do zmiennej.

```js
const add1 = (a) => a + 1
```

## Lambda Calculus

Gałąź matematyki, która wykorzystuje funkcje, aby stworzyć [uniwersalny model obliczeń](https://en.wikipedia.org/wiki/Lambda_calculus).

## Lazy evaluation

Lazy evaluation to mechanizm wartościowania typu call-by-need. Ewaluacja jest opóźniona do momentu, kiedy wartość jest potrzebna. W językach funkcyjnych pozwala to na struktury takie jak nieskończone listy, które normalnie nie byłyby dostępne w języku imperatywnym, gdzie sekwencjonowanie poleceń jest istotne.

```js
const rand = function*() {
  while (1 < 2) {
    yield Math.random()
  }
}
```

```js
const randIter = rand()
randIter.next() // Każde wywołanie zwraca losową wartość. Wyrażenie jest obliczane wtedy, kiedy jest potrzebne.
```

## Monoid

Obiekt z funkcją, która "łączy" ten obiekt z innym obiektem tego samego typu.

Przykładem prostego monoidu jest dodawanie liczb:

```js
1 + 1 // 2
```
W tym przypadku liczba jest obiektem, a `+` jest funkcją.

Musi również istnieć wartość tożsamości (identity value) - taka, że kiedy połączyć ją z wartością, to ona się nie zmienia. Zobacz poniżej:

Wartością tożsamości dla dodawania jest `0`.
```js
1 + 0 // 1
```

Jest także wymagane to, żeby grupowanie operacji nie wpływało na wynik (asocjatywność):

```js
1 + (2 + 3) === (1 + 2) + 3 // true
```

Konkatenacja tablic również tworzy monoid:

```js
;[1, 2].concat([3, 4]) // [1, 2, 3, 4]
```

W tym przypadku wartością tożsamości jest pusta tablica `[]`

```js
;[1, 2].concat([]) // [1, 2]
```

Jeśli mamy funkcje tożsamości oraz kompozycji (obie), one także tworzą monoid:

```js
const identity = (a) => a
const compose = (f, g) => (x) => f(g(x))
```
`foo` jest dowolną jednoargumentową funkcją.
```
compose(foo, identity) ≍ compose(identity, foo) ≍ foo
```

## Monad

Monada jest obiektem z funkcją [`of`](#pointed-functor) oraz `chain`. `chain` jest jak [`map`](#functor) z tym wyjątkiem, że "odgnieżdża" zagnieżdżony obiekt.

```js
// Implementacja
Array.prototype.chain = function (f) {
  return this.reduce((acc, it) => acc.concat(f(it)), [])
}

// Użycie
Array.of('cat,dog', 'fish,bird').chain((a) => a.split(',')) // ['cat', 'dog', 'fish', 'bird']

// Różnica w stosunku do map
Array.of('cat,dog', 'fish,bird').map((a) => a.split(',')) // [['cat', 'dog'], ['fish', 'bird']]
```

`of` w innych językach funkcyjnych jest znane także jako `return`
`chain` w innych językach jest znane także jako `flatmap` i `bind`

## Comonad

Comonad to obiekt, który ma funkcje `extract` oraz `extend`.

```js
const CoIdentity = (v) => ({
  val: v,
  extract () {
    return this.val
  },
  extend (f) {
    return CoIdentity(f(this))
  }
})
```

`extract` wydobywa wartość z funktora.

```js
CoIdentity(1).extract() // 1
```

`extend` wykonuje funkcję na comonadzie. Funkcja ta powinna zwracać ten sam typ, jakim jest ten comonad.

```js
CoIdentity(1).extend((co) => co.extract() + 1) // CoIdentity(2)
```

## Applicative Functor

Funktorem aplikacyjnym jest obiekt z funkcją `ap`. Funkcja ta aplikuje funkcję z tego obiektu do wartości innego obiektu tego samego typu.

```js
// Implementacja
Array.prototype.ap = function (xs) {
  return this.reduce((acc, f) => acc.concat(xs.map(f)), [])
}

// Pzykładowe użycie
;[(a) => a + 1].ap([1]) // [2]
```

Jest to użyteczne, kiedy masz dwa obiekty i chcesz zaaplikować funkcję binarną do ich zawartości.

```js
// Tablice, które chcesz połączyć:
const arg1 = [1, 3]
const arg2 = [4, 5]

// łącząca funkcja - w tym przypadku trzeba zastosować currying, aby działało, jak trzeba
const add = (x) => (y) => x + y

const partiallyAppliedAdds = [add].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
```

W wyniku otrzymujesz tablicę funkcji, na której możesz wywołać `ap`, aby otrzymać wynik:

```js
partiallyAppliedAdds.ap(arg2) // [5, 6, 7, 8]
```

## Morphism

Morfizm to funkcja transformacji.

### Endomorphism

Endomorfizm to funkcja, której typ wejściowy (input) jest taki sam, jak jej typ wyjścia (output).

```js
// uppercase :: String -> String
const uppercase = (str) => str.toUpperCase()

// decrement :: Number -> Number
const decrement = (x) => x - 1
```

### Isomorphism

Izomorfizm to para przekształceń między 2 typami obiektów, która jest strukturalna z natury i żadne dane nie są utracone.

Na przykład: współrzędne 2D mogłyby być przechowywane jako tablica `[2,3]` lub obiekt `{x: 2, y: 3}`.

```js
// Dostarczenie funkcji do konwersji w obu kierunkach czyni je izomorficznymi.
const pairToCoords = (pair) => ({x: pair[0], y: pair[1]})

const coordsToPair = (coords) => [coords.x, coords.y]

coordsToPair(pairToCoords([1, 2])) // [1, 2]

pairToCoords(coordsToPair({x: 1, y: 2})) // {x: 1, y: 2}
```



## Setoid

Setoid to obiekt, który ma funkcję `equals`, która może być użyta, aby porównać inne obiektu tego samego typu.

Spraw, żeby tablica była setoidem:

```js
Array.prototype.equals = function (arr) {
  const len = this.length
  if (len !== arr.length) {
    return false
  }
  for (let i = 0; i < len; i++) {
    if (this[i] !== arr[i]) {
      return false
    }
  }
  return true
}

;[1, 2].equals([1, 2]) // true
;[1, 2].equals([0]) // false
```

## Semigroup

Obiekt, który ma funkcję `concat`, która łączy go z innym obiektem tego samego typu.

```js
;[1].concat([2]) // [1, 2]
```

## Foldable

Obiekt, który ma funkcję `reduce`, która może przekształcić ten obiekt do jakiegoś innego typu.

```js
const sum = (list) => list.reduce((acc, val) => acc + val, 0)
sum([1, 2, 3]) // 6
```

## Lens ##
A lens is a structure (often an object or function) that pairs a getter and a non-mutating setter for some other data
structure.

```js
// Using [Ramda's lens](http://ramdajs.com/docs/#lens)
const nameLens = R.lens(
  // getter for name property on an object
  (obj) => obj.name,
  // setter for name property
  (val, obj) => Object.assign({}, obj, {name: val})
)
```

Having the pair of get and set for a given data structure enables a few key features.

```js
const person = {name: 'Gertrude Blanch'}

// invoke the getter
R.view(nameLens, person) // 'Gertrude Blanch'

// invoke the setter
R.set(nameLens, 'Shafi Goldwasser', person) // {name: 'Shafi Goldwasser'}

// run a function on the value in the structure
R.over(nameLens, uppercase, person) // {name: 'GERTRUDE BLANCH'}
```

Lenses are also composable. This allows easy immutable updates to deeply nested data.

```js
// This lens focuses on the first item in a non-empty array
const firstLens = R.lens(
  // get first item in array
  xs => xs[0],
  // non-mutating setter for first item in array
  (val, [__, ...xs]) => [val, ...xs]
)

const people = [{name: 'Gertrude Blanch'}, {name: 'Shafi Goldwasser'}]

// Despite what you may assume, lenses compose left-to-right.
R.over(compose(firstLens, nameLens), uppercase, people) // [{'name': 'GERTRUDE BLANCH'}, {'name': 'Shafi Goldwasser'}]
```

Other implementations:
* [partial.lenses](https://github.com/calmm-js/partial.lenses) - Tasty syntax sugar and a lot of powerful features
* [nanoscope](http://www.kovach.me/nanoscope/) - Fluent-interface

## Type Signatures

Often functions in JavaScript will include comments that indicate the types of their arguments and return values.

There's quite a bit of variance across the community but they often follow the following patterns:

```js
// functionName :: firstArgType -> secondArgType -> returnType

// add :: Number -> Number -> Number
const add = (x) => (y) => x + y

// increment :: Number -> Number
const increment = (x) => x + 1
```

If a function accepts another function as an argument it is wrapped in parentheses.

```js
// call :: (a -> b) -> a -> b
const call = (f) => (x) => f(x)
```

The letters `a`, `b`, `c`, `d` are used to signify that the argument can be of any type. The following version of `map` takes a function that transforms a value of some type `a` into another type `b`, an array of values of type `a`, and returns an array of values of type `b`.

```js
// map :: (a -> b) -> [a] -> [b]
const map = (f) => (list) => list.map(f)
```

__Further reading__
* [Ramda's type signatures](https://github.com/ramda/ramda/wiki/Type-Signatures)
* [Mostly Adequate Guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html#whats-your-type)
* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) on Stack Overflow

## Algebraic data type
A composite type made from putting other types together. Two common classes of algebraic types are [sum](#sum-type) and [product](#product-type).

### Sum type
A Sum type is the combination of two types together into another one. It is called sum because the number of possible values in the result type is the sum of the input types.

JavaScript doesn't have types like this but we can use `Set`s to pretend:
```js
// imagine that rather than sets here we have types that can only have these values
const bools = new Set([true, false])
const halfTrue = new Set(['half-true'])

// The weakLogic type contains the sum of the values from bools and halfTrue
const weakLogicValues = new Set([...bools, ...halfTrue])
```

Sum types are sometimes called union types, discriminated unions, or tagged unions.

There's a [couple](https://github.com/paldepind/union-type) [libraries](https://github.com/puffnfresh/daggy) in JS which help with defining and using union types.

Flow includes [union types](https://flow.org/en/docs/types/unions/) and TypeScript has [Enums](https://www.typescriptlang.org/docs/handbook/enums.html) to serve the same role.

### Product type

A **product** type combines types together in a way you're probably more familiar with:

```js
// point :: (Number, Number) -> {x: Number, y: Number}
const point = (x, y) => ({ x, y })
```
It's called a product because the total possible values of the data structure is the product of the different values. Many languages have a tuple type which is the simplest formulation of a product type.

See also [Set theory](https://en.wikipedia.org/wiki/Set_theory).

## Option
Option is a [sum type](#sum-type) with two cases often called `Some` and `None`.

Option is useful for composing functions that might not return a value.

```js
// Naive definition

const Some = (v) => ({
  val: v,
  map (f) {
    return Some(f(this.val))
  },
  chain (f) {
    return f(this.val)
  }
})

const None = () => ({
  map (f) {
    return this
  },
  chain (f) {
    return this
  }
})

// maybeProp :: (String, {a}) -> Option a
const maybeProp = (key, obj) => typeof obj[key] === 'undefined' ? None() : Some(obj[key])
```
Use `chain` to sequence functions that return `Option`s
```js

// getItem :: Cart -> Option CartItem
const getItem = (cart) => maybeProp('item', cart)

// getPrice :: Item -> Option Number
const getPrice = (item) => maybeProp('price', item)

// getNestedPrice :: cart -> Option a
const getNestedPrice = (cart) => getItem(cart).chain(getPrice)

getNestedPrice({}) // None()
getNestedPrice({item: {foo: 1}}) // None()
getNestedPrice({item: {price: 9.99}}) // Some(9.99)
```

`Option` is also known as `Maybe`. `Some` is sometimes called `Just`. `None` is sometimes called `Nothing`.

## Functional Programming Libraries in JavaScript

* [mori](https://github.com/swannodette/mori)
* [Immutable](https://github.com/facebook/immutable-js/)
* [Ramda](https://github.com/ramda/ramda)
* [ramda-adjunct](https://github.com/char0n/ramda-adjunct)
* [Folktale](http://folktale.origamitower.com/)
* [monet.js](https://cwmyers.github.io/monet.js/)
* [lodash](https://github.com/lodash/lodash)
* [Underscore.js](https://github.com/jashkenas/underscore)
* [Lazy.js](https://github.com/dtao/lazy.js)
* [maryamyriameliamurphies.js](https://github.com/sjsyrek/maryamyriameliamurphies.js)
* [Haskell in ES6](https://github.com/casualjavascript/haskell-in-es6)
* [Sanctuary](https://github.com/sanctuary-js/sanctuary)
* [Crocks](https://github.com/evilsoft/crocks)

---

__P.S:__ This repo is successful due to the wonderful [contributions](https://github.com/hemanth/functional-programming-jargon/graphs/contributors)!
