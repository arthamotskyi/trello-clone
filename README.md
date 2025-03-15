# Проєкт 1(Trello-Clone)
## Розробка та контроль якості веб-додатка для управління задачами
## Опис:
Необхідно розробити веб- або десктоп-додаток для управління задачами (аналог Trello). Додаток дозволяє створювати, редагувати та переміщати задачі між списками.

# 1. Модель якості

## Модель якості згідно з ISO/IEC 25010 та ISO/IEC 25019:2023

| **Характеристика**          | **Підхарактеристики**     | **Опис** | **Пріоритет** |
|----------------------------|-------------------------|------------------------------------------------------------|-----------------|
| **Функціональна придатність** | Повнота функцій | Додаток дозволяє створювати, редагувати, видаляти та переміщати задачі між списками. Дані зберігаються у `localStorage`. | Високий |
|  | Коректність результатів | Всі дії користувача виконуються без помилок: переміщення задач, їх редагування та збереження. | Високий |
|  | Придатність для взаємодії | Є можливість експорту/імпорту даних у JSON, але відсутня інтеграція з хмарними сервісами. | Середній |
| **Продуктивність** | Час відгуку | Завантаження головної сторінки — до 2 с, зміна статусу задачі — < 200 мс. | Високий |
|  | Використання ресурсів | Споживання RAM у браузері — 150-200 МБ. Використання `localStorage` < 1 МБ. | Середній |
| **Сумісність** | Кросбраузерність | Підтримка всіх сучасних браузерів (Chrome, Firefox, Edge). | Високий |
| **Зручність використання** | Інтуїтивність | Простий drag-and-drop інтерфейс. Відсутні непотрібні кроки при створенні/редагуванні задач. | Високий |
| **Надійність** | Відновлення даних | Дані зберігаються у `localStorage` після перезавантаження сторінки. Відсутній механізм резервного копіювання. | Середній |
| **Захищеність** | Безпека введених даних | Відсутня перевірка введених даних на XSS-уразливості. | Низький |
| **Обслуговуваність** | Якість коду | Код організований згідно з Vue-компонентами, використані принципи SOLID. | Високий |
| **Переносимість** | Можливість розгортання | Додаток працює як PWA, але немає можливості роботи в офлайн-режимі. | Середній |

## Ключові метрики якості коду

- **Читабельність коду**: відповідність коду стилю `ESLint` та `Prettier`.
- **Структурованість**: використання компонентного підходу у Vue.js.
- **Перевірка помилок**: тестування основних функцій через Jest/Vitest.
- **Продуктивність**: середній час виконання основних операцій (`< 200 мс`).
- **Безпека**: відсутність XSS-вразливостей та захист від CSRF.
- **Розширюваність**: можливість додавання нових функцій без значних змін в архітектурі.

# 2. Формальна верифікація специфікацій

### Специфікація обмежень
Для забезпечення коректності роботи Trello-Clone я сформулював наступні обмеження:

- Кожен користувач має хоча б одну задачу.
- Кожна задача входить лише до одного списку.
- Користувач може створювати кілька списків, але кожен список належить лише одному користувачеві.
- Завдання можуть змінювати свій статус (переміщуватися між списками).

### Перевірка обмежень у Alloy
Для формальної верифікації цих обмежень використовується Alloy Analyzer. Нижче наведена модель, яка перевіряє відповідність зазначеним правилам.

```alloy
module TrelloClone

-- Визначення об'єктів
sig User {
    lists: set List
}

sig List {
    tasks: set Task,
    owner: one User
}

sig Task {
    belongsTo: one List
}

-- Обмеження
fact Constraints {
    -- Кожен користувач має хоча б один список
    all u: User | some u.lists  

    -- Кожен список належить лише одному користувачеві
    all l: List | one l.owner  

    -- Кожна задача належить тільки до одного списку
    all t: Task | one t.belongsTo  

    -- Кожен користувач має хоча б одну задачу
    all u: User | some t: Task | t.belongsTo in u.lists  
}

-- Запуск моделі для перевірки
run {} for 5
```

### Результати роботи Alloy Analyzer
#### Виконав команду `Run` для перевірки коректності специфікацій і отримав результат:
![image](https://github.com/user-attachments/assets/53e8abcb-e4a8-445b-8530-69726bd9825d)
![image](https://github.com/user-attachments/assets/bbcb24a2-4562-49f1-96a2-41df77d4dd63)
#### Результат в Alloy означає, що специфікація коректна і має принаймні один можливий варіант виконання.
---
