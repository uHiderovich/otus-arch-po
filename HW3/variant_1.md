# Студент отправляет работу на проверку перед дедлайном

```
@startuml
skinparam actorStyle awesome

actor "Студент" as Student

box "Внутренние модули" #LightBlue
    participant "Проверка решений" as Check
    participant "Управление историей" as History
end box

participant "Сторонние сервисы" as External
participant "Проверка на плагиат" as Pl
participant "LMS" as LMS

Student -> Check : Работа на проверку

Check -> Check : Проверка дедлайна

alt Дедлайн не наступил
    Check -> History : Зафиксировать принятую попытку
    History --> Check : Попытка сохранена

    Check --> Student : Работа принята на проверку

    Check ->> Check : Запустить фоновую проверку

    group Фоновая проверка
        Check -> Check : Проверка по критериям

        Check -> External : Работа на проверку
        External -> Pl : Работа на проверку

        alt Pl вернул результат проверки
	        Pl --> External : % совпадения
	        External --> Check : % совпадения
		else
			External --> Check : Ошибка
		end

        Check -> History : Сохранить результат проверки
        History --> Check : Сохранение подтверждено

        Check -->> Student : Уведомление о результате

        Check -> LMS : Зафиксировать оценку

        alt LMS подтвердила сохранение
            LMS --> Check : Оценка успешно сохранена
            Check -> Check : Зафиксировать успешную передачу
        else LMS вернула ошибку
            LMS --> Check : Ошибка
            Check -> Check : Зафиксировать необходимость повтора
        else Время ожидания LMS истекло
            Check -> Check : Зафиксировать неподтверждённую передачу
        end
    end

else Дедлайн наступил
    Check --> Student : Отказ в приёме работы
end
@enduml
```
