# Об'єкт TraceCategoriesAndOptions

* `categoryFilter` String - A filter to control what category groups should be traced. A filter can have an optional '-' prefix to exclude category groups that contain a matching category. Не підтримується можливість мати як шаблони для включення так і для виключення груп категорій в одному списку. Приклади: `test_MyTest*`, `test_MyTest*,test_OtherStuff`, `-excluded_category1,-excluded_category2`.
* `traceOptions` String - Контролює спосіб відслідковування, це роздлена комами послідовність наступних стрічок: `record-until-full`, `record-continuously`, `trace-to-console`, `enable-sampling`, `enable-systrace`, наприклад, `'record-until-full,enable-sampling'`. Перші 3 опції режимами запису відслідковування і є взаємовиключними. Якщо більше одного режиму запису з'являється в стрічці `traceOptions`, перевага надається останньому. Якщо не визначений жоден режим запису, використовується `record-until-full`. Опція відслідковування скинеться до значення за замовчуванням (`record_mode` встановиться в `record-until-full`, `enable_sampling` і `enable_systrace` встановляться в `false`) перед застосуванням опцій з `traceOptions`.