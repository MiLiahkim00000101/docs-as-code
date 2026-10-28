# Диаграммы

## Диаграмма последовательности

Не прописываю доступ к базе данных. Он подразумевается в нашей системе, т.к. где-то надо хранить информацию об обработанных отзывах. Мы это не вкладываем в продукт, однако, наверняка у вас уже есть своя бд для хранения отзывов. Туда же можно класть и результаты нашей системы.

```mermaid
sequenceDiagram
    actor User1 as Пользователь1
    actor User2 as Пользователь2
    actor User_l as Производитель

    participant System_prep as Система 
    participant Model as ML-модель


    User1->>System_prep: Отправить отзыв
    System_prep->>Model: Передать отзыв
    Model-->>System_prep: Вернуть тон

    User2->>System_prep: Отправить отзыв
    System_prep->>Model: Передать отзыв
    Model-->>System_prep: Вернуть тон

    User_l->>System_prep: Запросить аналитику
    System_prep-->>User_l: Показать средний тон по отправленным отзывам
```
## Диаграмма классов

```mermaid
classDiagram

    class User {
        +int id
        +string name
    }

    class Review {
        +int user_id
        +string text
    }

    class ClassificationRequest {
        +int id
        +int review_ids
        +string tone
    }

    class ClassificationResult {
        +int request_id
        +string tone
        +float confidence
    }

    class AnalyticsRequest {
        +int id
        +int classification_ids
    }

    class AnalyticsResult {
        +int id
        +int req_id
        +float result
    }

    User "1" --> "*" Review
    Review "1" --> "1" ClassificationRequest
    ClassificationRequest "1" --> "1" ClassificationResult
    AnalyticsRequest "1" <-- "*" ClassificationResult
    AnalyticsResult "1" <-- "1" AnalyticsRequest




```
