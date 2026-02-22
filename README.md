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

# ошибка 3 

когда проводил docker compose up --build вылетела ошибка 
```
File "/app/app/services/parser.py", line 43, in parse_and_store
app-1  |     "city_name": item.city.name.strip(),
app-1  |                  ^^^^^^^^^^^^^^
app-1  | AttributeError: 'NoneType' object has no attribute 'name'
```
по такому то пути объект не содержит атрибут имя
исправил на обработку имени
```
"city_name": item.city.name.strip() if item.city and item.city.name else None,
```
Если объект city существует и у него есть поле name,
то берём имя города 
Если города нет или имя отсутствует  записываем None

# ошибка 4
Открыл чтобы посмотреть в моделях , что происходит 
по пути  selectest-api\app\models\vacancy.py
есть уникальное поле external_id: Mapped[int | None] = mapped_column(Integer, nullable=True)
которое позволяет делать null бесконечное количество раз 
исправил на nullable false 
ДО
```
    external_id: Mapped[int | None] = mapped_column(Integer, nullable=True)

```

ПОСЛЕ

```
    external_id: Mapped[int | None] = mapped_column(Integer, nullable=False)

```

# Ошибка 5 
не стоит uniq есть уникальное поле external_id: Mapped[int | None] = mapped_column(Integer, nullable=False)

добавил unique=True,

```
external_id: Mapped[int | None] = mapped_column(Integer, nullable=False , unique=True,)
```



# ошибка номер 6 
в req text дважды дублируется fast api  и он пытается установить несуществующую версию , удаляю

```
fastapi==999.0.0; python_version < "3.8"
```
## запускаю сборку перехожу на сайт

# Отображение работает все поля видны, всё четко. 
# создание работает успешно повторное создание с таким же внешним айди выдает ошибку
```
{
  "detail": "Vacancy with external_id already exists"
}
```
и не создаёт , но вылетает ендпоинт об успехе а не об ошибке, нужно исправить 

#
