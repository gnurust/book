## Переменные и понятие изменяемости

Как было указано в главе 2, по умолчанию все Rust переменные являются неизменяемыми. Это одна из особенностей языка Rust, которая позволяет писать код так, чтобы получить предоставляемые преимущества безопасности и лёгкого  решения задач параллельного программирования. Тем не менее, у вас остаётся возможность сделать переменные изменяемыми. Давайте рассмотрим как и почему Rust поощряет неизменность (immutability) и почему иногда вы можете отказаться от этого.

Когда переменная неизменяемая, то её значение нельзя менять, как только значение привязано к её имени. Приведём пример использования этого типа переменной. Для этого создадим новый проект, назовём его *variables* в каталоге *projects*, создадим с помощью команды: `cargo new --bin variables`.

Потом в созданной папке проекта *variables* откройте исходный файл *src/main.rs* и замените следующим кодом, который пока не будет компилироваться:

<span class="filename">Файл: src/main.rs</span>

```rust,ignore,does_not_compile
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

Сохраните код программы и выполните команду `cargo run`. В командной строке вы увидите сообщение об ошибке:

```text
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     println!("The value of x is: {}", x);
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
```

Данный пример показывает как компилятор помогает найти ошибки в программах. Несмотря на то, что ошибки компилятора могут вызывать разочарование, они означают, что ваша программа ещё не выполняет то, что вы от неё хотите. Ошибки *не означают*, что вы пока не являетесь хорошим программистом! Опытные разработчики Rust также получают ошибки компиляции.

Описание ошибки даёт понять, что причиной является - `попытка присвоить неизменяемой переменной новое значение`, потому что вы попытались назначить второе значение неизменяемой переменной  `x`.

Это важно, что мы получили ошибки во время компиляции, при попытке изменить ранее не изменяемое значение, потому что такая ситуация может привести к дефектам в программе. Если одна часть кода работает с предположением, что данное значение никогда не изменится, а другая часть кода все-таки меняет значение, всегда возможно, что первая часть кода не будет делать того, для чего она была предназначена. Причину дефектов такого рода иногда трудно отследить, после того как изменение произошло, особенно когда вторая часть кода меняет значение только *иногда*.

В Rust, компилятор гарантирует, что если указано, что значение не будет меняется, то оно действительно не будет меняется. Это означает, что когда вы читаете или пишите код, вам не нужно отслеживать то, как и где значение может изменится. В таком случае ваш код легче понимать.

Но изменяемость может быть очень полезной. Переменные являются не изменяемыми только по умолчанию. Аналогично как вы делали в главе 2, можно сделать переменные изменяемыми добавлением ключевого слова `mut` перед названием переменной. В дополнение к возможности изменить значение, указание `mut` передаёт намерение будущим читателям кода, что другие части кода будут изменять значение этой переменной.

Например, изменим *src/main.rs* на следующий код:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

Запустив программу, мы получим результат:

```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/variables`
The value of x is: 5
The value of x is: 6
```

Мы разрешили изменять значение, которое назначили `x` со значения `5` на значение `6`, когда использовано `mut`. В некоторых случаях, вам захочется сделать переменную изменяемой, потому что так становится проще писать код, в отличии от случая, когда есть только неизменяемые переменные.

Следует учитывать множество дополнительных компромиссов для  предотвращения дефектов. Например, для повышения производительности кода при работе с большими структурами данных, изменение экземпляра на месте может быть быстрее, чем копирование и возврат вновь созданного экземпляра. При небольших структурах данных создание новых экземпляров и запись кода в более функциональном стиле, может оказаться проще для понимания. В таком случае, снижение производительности может быть полезным недостатком в пользу большей ясности кода.

### Различия между переменными и константами

Невозможность изменить значение переменной может напомнить другую концепцию программирования, которая есть в других языках: *константы (constants)*. Подобно не изменяемым переменным, константы являются значениями привязанными к именам, которые не разрешено менять, но есть несколько отличий между константами и не изменяемыми переменными.

Первое, это то что с константами не разрешено использовать `mut`. Константы не являются не изменяемыми по умолчанию — они не изменяемые всегда.

Константы объявляются с помощью ключевого слова `const` вместо ключевого слова `let` и тип значения *должен* быть аннотирован. Мы собираемся объяснить типы и аннотации типов в следующем разделе [“Типы данных”]<comment>, так что не волнуйтесь про детали сейчас. Просто знайте, что тип нужно всегда аннотировать.</comment>

Константы можно объявить в любой области видимости, включая глобальный. Это делает их удобными для значений, про которые должны знать многие другие части кода.

Последней разницей является то, что константы можно установить только в константное выражение, которое не является результатом вызова функции или любым другим значением, которое можно посчитать только во время выполнения.

Вот пример объявления константы с названием `MAX_POINTS` значение которой установлено в 100,000. Для объявления констант рекомендуется использовать все заглавные буквы с подчёркиванием между словами. Также подчёркивание можно вставлять в цифровые литералы для улучшения чтения.

```rust
const MAX_POINTS: u32 = 100_000;
```

Константы являются корректными для всего времени выполнения программы, внутри области видимости где они были объявлены. Это  делает их удобным выбором для значений в приложении, которые могут быть доступны для многих частей приложения. Например, максимально разрешённое количество очков игрока в игре или скорость света в вакууме.

Наименование не изменяемых значений во всей программе, таких как константа, является удобным способом выразить смысл значения для будущих пользователей кода. Этот помогает иметь только одно место в коде, которое придётся обновить, если будет необходимо поменять его значение в будущем.

### Затенение (переменных)

Как вы видели в обучающем материале по угадыванию числа из раздела [“Сравнение предположения с секретным числом”](ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number)<comment> главы 2, можно объявить переменную с тем же именем что была раньше и такая новая переменная затеняет предыдущую.
Rust разработчики говорят, что первая переменная <em>затенена (shadowed)</em> второй, а значение второй переменной появляется в коде, когда её используют. Можно затенять переменную, используя тоже самое имя и повторяя использование ключевого слова <code>let</code> как в примере:</comment>

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    let x = x * 2;

    println!("The value of x is: {}", x);
}
```

Программа сначала связывает значение `5` с переменной `x`. Затем `x` затеняется повторением кода  `let x =` с помощью начального значения и прибавления к нему `1`, так что значение `x` становится равным `6`. Третье выражение `let` также затеняет переменную `x`, умножением предыдущее значение на `2`. Это даёт переменной `x` финальное значение равное `12`. При запуске программы мы получим вывод:

```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/variables`
The value of x is: 12
```

Затенение отличается от объявления переменной с помощью `mut`, так как мы получим ошибку компиляции, если случайно попробуем переназначить значение без использования ключевого слова `let`. Используя `let`, можно выполнить несколько превращений над значением, при этом оставляя переменную неизменяемой, после того как все эти превращения завершены.

Другой разницей между `mut` и затенением является то, что мы создаём совершенно новую переменную, когда снова используем слово `let`. Мы можем даже изменить тип значения, но снова использовать предыдущее имя. К примеру, наша программа спрашивает пользователя сколько пробелов он хочет разместить между некоторым текстом, запрашивая символы пробела, но мы на самом деле хотим сохранить данный ввод как число:

```rust
let spaces = "   ";
let spaces = spaces.len();
```

Данная конструкция разрешена, потому что первая переменная `spaces` является строковым типом, а вторая переменная `spaces` числовым. Она является совершенно новой переменной с одинаковым именем, что было у первой. Таким образом, затенение избавляет нас от необходимости придумывать разные имена, вроде `spaces_str` и  `spaces_num`. Вместо этого можно использовать снова более простое имя `spaces`.
Тем не менее, если попробовать использовать для этого `mut` как показано ниже, то мы получим ошибку компиляции:

```rust,ignore,does_not_compile
let mut spaces = "   ";
spaces = spaces.len();
```

Ошибка говорит, что не разрешается менять тип переменной:

```text
error[E0308]: mismatched types
 --> src/main.rs:3:14
  |
3 |     spaces = spaces.len();
  |              ^^^^^^^^^^^^ expected &str, found usize
  |
  = note: expected type `&str`
             found type `usize`
```

Теперь, когда вы имеете представление о работе с переменными, посмотрим на большее количество типов данных, которые они могут иметь.


[“Типы данных”]: ch03-02-data-types.html#data-types