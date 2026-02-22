# selectel-Test-task
### UPD Я до этого разбирался несколько часов, а сейчас типо красивое описание делаю , я не гений который всё за 10 минут сделал.
# 1 Ошибка
скачал архив, запустил docker compose up --build, так как env есть , и yml  на том же уровне ,увидел ошибку 

```
pydantic_core._pydantic_core.ValidationError: 1 validation error for Settings
app-1  | database_url
```
, пошёл искать settings побегал по файлам, зашёл  app/core/config , увидел ошибку 

Исправил с
```
        validation_alias="DATABSE_URL",
```
на
```
        validation_alias="DATABASE_URL",
```

# 2 Ошибка 

Обновляется каждые 5 секунд , а не минут как написано в задании , пошёл исправлять 

зашел в selectest-api\app\services\scheduler.py

Исправил с 

```
seconds=settings.parse_schedule_minutes,
```
на 
```
minutes==settings.parse_schedule_minutes,
```
