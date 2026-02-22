# selectel-Test-task
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

