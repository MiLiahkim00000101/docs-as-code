# Диаграммы

## Диаграмма последовательности

Не прописываю доступ к базе данных. Он подразумевается в нашей системе, т.к. где-то надо хранить информацию об обработанных отзывах. Мы это не вкладываем в продукт, однако, наверняка у вас уже есть своя бд для хранения отзывов. Туда же можно класть и результаты нашей системы.

```mermaid
sequenceDiagram
    actor Producer as Производитель
    participant System as Система
    participant Model as ML-модель

    Producer->>System: 1. Загрузить отзывы о товаре
    Producer->>System: 2. Выбрать способ расчёта (с нейтральными / без)

    loop 3-4. Для каждого отзыва
        System->>Model: Передать текст отзыва по API
        alt отзыв классифицирован
            Model-->>System: Вернуть тональность (негативная / нейтральная / позитивная)
        else 4а. не удалось классифицировать
            Model-->>System: Ошибка классификации
            System->>System: Пометить «не классифицирован», исключить из расчёта
        end
    end

    Note over System: 5. Расчёт средней тональности (UC-04)
    System->>System: Исключить неклассифицированные отзывы
    System->>System: Посчитать количество и долю по тональностям
    System->>System: Сопоставить значения: негативная = 0, нейтральная = 1, позитивная = 2
    System->>System: Если выбран способ «без нейтральных», исключить нейтральные

    alt в выборке есть хотя бы один отзыв
        System->>System: Вычислить среднее арифметическое
        System-->>Producer: 6. Тональность каждого отзыва, средняя тональность, распределение
    else 5а. выборка пуста
        System-->>Producer: 6. Тональность каждого отзыва и сообщение «Недостаточно данных для расчёта»
    end

    System-->>Producer: Список неклассифицированных отзывов для ручной проверки
```

## Диаграмма классов
```mermaid
classDiagram
    class Product {
        +int id
        +string name
    }

    class Review {
        +int id
        +int product_id
        +int author_id
        +string text
    }

    class ReviewClassification {
        +int id
        +int review_id
        +string status
        +string tone
        +float confidence
    }

    class AnalyticsRequest {
        +int id
        +int product_id
        +bool include_neutral
    }

    class AnalyticsResult {
        +int id
        +int request_id
        +string status
        +float average
        +int negative_count
        +int neutral_count
        +int positive_count
    }

    Product "1" --> "*" Review
    Review "1" --> "0..1" ReviewClassification
    AnalyticsRequest "1" --> "1" Product
    AnalyticsRequest "1" --> "1" AnalyticsResult
    AnalyticsResult "1" ..> "*" ReviewClassification : построен по
```