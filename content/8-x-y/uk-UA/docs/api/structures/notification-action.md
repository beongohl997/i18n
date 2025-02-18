# Об'єкт NotificationAction

* `type` String - Тип дії, може бути `button`.
* `text` String (опціонально) - Назва для переданої дії.

## Платформа / Підтримка дії

| Тип дії  | Підтримка платформами | Використання `text`                  | `text` за замовчуванням                                                                     | Обмеження                                                                                                                                                                                                                                                        |
| -------- | --------------------- | ------------------------------------ | ------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `button` | macOS                 | Використовується як напис для кнопки | "Show" (чи локалізована системою стрічка, якщо перша така `button`, в іншому випадку пусто) | Використовується тільки перша. Якщо передбачено декілька, всі крім першої будуть вважатися додатковими діями (відображені коли мишка наведена на активну кнопку). Будь-яка така дія також несумісна з `hasReply` і буде проігнорована, якщо `hasReply` є `true`. |

### Button підтримка на macOS

Щоб додаткові кнопки повідомлень працювали на macOS, ваш додаток має відповідати наступним вимогам.

* Застосунок підписано
* Застосунок має `NSUserNotificationAlertStyle` встановлений в `alert` в `Info.plist`.

Якщо жодне з цих вимог не буде виконано, кнопка не з'явиться.
