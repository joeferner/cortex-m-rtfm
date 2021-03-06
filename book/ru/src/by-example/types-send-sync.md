# Типы, Send и Sync

Атрибут `app` вводит контекст, коллекцию переменных в каждую из функций.
Все эти переменные имеют предсказуемые, неанонимные типы, поэтому Вы можете
писать простые функции, получающие их как аргументы.

Описание API определяет как эти типы эти типы генерируются из входных данных.
Вы можете также сгенерировать документацию для Вашей бинарной библиотеки
(`cargo doc --bin <name>`); в документации Вы найдете структуры `Context`
(например `init::Context` и `idle::Context`), чьи поля представляют переменные
включенные в каждую функцию.

В примере ниже сгенерированы разные типы с помощью атрибута `app`.

``` rust
{{#include ../../../../examples/types.rs}}
```

## `Send`

[`Send`] - маркерный типаж (trait) для "типов, которые можно передавать через границы
потоков", как это определено в `core`. В контексте RTFM типаж `Send` необходим
только там, где возможна передача значения между задачами, запускаемыми на
*разных* приоритетах. Это возникает в нескольких случаях: при передаче сообщений,
в совместно используемых `static mut` ресурсах и инициализации поздних ресурсов.

[`Send`]: https://doc.rust-lang.org/core/marker/trait.Send.html

Атрибут `app` проверит, что `Send` реализован, где необходимо, поэтому Вам не
стоит волноваться об этом. Более важно знать, где Вам *не* нужен типаж `Send`:
в типах, передаваемых между задачами с *одинаковым* приоритетом. Это возникает
в двух случаях: при передаче сообщений и в совместно используемых `static mut`
ресурсах.

В примере ниже показано, где можно использовать типы, не реализующие `Send`.

``` rust
{{#include ../../../../examples/not-send.rs}}
```

## `Sync`

Похожая ситуация, [`Sync`] -  маркерный типаж для "типов, на которых можно
ссылаться в разных потоках", как это определено в `core`. В контексте RTFM
типаж `Sync` необходим только там, где возможны две или более задачи,
запускаемые на разных приоритетах, чтобы захватить разделяемую ссылку на
ресурс. Это возникает только  совместно используемых `static`-ресурсах.

[`Sync`]: https://doc.rust-lang.org/core/marker/trait.Sync.html

Атрибут `app` проверит, что `Sync` реализован, где необходимо, но важно знать,
где ограничение `Sync` не требуется: в `static`-ресурсах, разделяемых между
задачами с *одинаковым* приоритетом.

В примере ниже показано, где можно использовать типы, не реализующие `Sync`.

``` rust
{{#include ../../../../examples/not-sync.rs}}
```
