# selectel-Test-task
### UPD Я до этого разбирался несколько часов, а сейчас типо красивое описание делаю , я не гений который всё за 10 минут сделал.
## Для 7-8 Я использовал ИИ. у меня не было знаний о фаст , апи, и сильно чтобы код не кроить и не изобретать велосипед:
- Разобрал ошибку с помощью ИИ. 
- С помощью ИИ внёс проверки, тк знаний на указанный параметр, и проверку не имел. С фаст апи не работал до этого момента (один раз с помошью ии, и то не в счёт)
## Остальное 
- остальные шаги делал сам смотря просто на вывод из консоли, она зачастую и путь пишет, где нужно было null указал, и прочее, 
- половина ошибок была прописано в задание, их сразу и проверил req text, и скорость обновления. единственное что, архитектура незнакомая , помучался и поискал
- далее подробный разбор ошибок, + фото как я это всё находил и исправлял.
- фото swagger UI в папке Images в корне проекта
- Проект полностью рабочий : Все эндпоинты работают корректно.
* Приложение возвращает корректные HTTP-статусы и данные.
* Приложен скриншот Swagger UI с выполненными успешными
запросами.

# ошибка номер 1
в req text дважды дублируется fast api  и он пытается установить несуществующую версию , удаляю .


```
fastapi==999.0.0; python_version < "3.8"
```

![alt text](<images/errors/ошибка req text.jpg>)

# 2 Ошибка
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
ошибка



![alt text](<images/errors/ошибка database.jpg>)
![alt text](<images/errors/ошикба database1.jpg>)

фикс




![alt text](<images/fix/исправлено database.jpg>)
# 3 Ошибка 

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
ошибка 



![alt text](<images/errors/ошибка pasertime.jpg>)



исправлено 



![alt text](<images/fix/исправлено parsertime.jpg>)

# ошибка 4

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

ошибка



![alt text](<images/errors/ошибка notname.jpg>)
![alt text](<images/errors/ошибка notname1.jpg>)



исправлено



![alt text](<images/fix/исправлено notname.jpg>)

# ошибка 5
Открыл чтобы посмотреть в моделях , что происходит 
по пути  selectest-api\app\models\vacancy.py
есть уникальное поле external_id: Mapped[int | None] = mapped_column(Integer, nullable=True)
которое позволяет делать null бесконечное количество раз 
исправил на nullable false  
* UPD тут загуглил, прав или нет, ибо модели в django строил пользовался uniq  и для админки выводы
ДО
```
    external_id: Mapped[int | None] = mapped_column(Integer, nullable=True)

```

ПОСЛЕ

```
    external_id: Mapped[int | None] = mapped_column(Integer, nullable=False)

```
ошибка 


![alt text](<images/errors/ошибка null = бесконечность.jpg>)



исправлено



![alt text](<images/fix/испрвлено нулл, адд юник.jpg>)


# Ошибка 6
не стоит uniq есть уникальное поле external_id: Mapped[int | None] = mapped_column(Integer, nullable=False)

добавил unique=True,

```
external_id: Mapped[int | None] = mapped_column(Integer, nullable=False , unique=True,)
```

ошибка 




![alt text](<images/errors/ошибка null = бесконечность.jpg>)



исправлено




![alt text](<images/fix/испрвлено нулл, адд юник.jpg>)



## запускаю сборку перехожу на сайт

# Отображение работает все поля видны, всё четко. 
# создание работает успешно повторное создание с таким же внешним айди выдает ошибку
```
{
  "detail": "Vacancy with external_id already exists"
}
```

# ошибка номер 7-8 update не работает  не работает 

```
500
Undocumented
Error: Internal Server Error

Response body
Download
Internal Server Error
Response headers
 content-length: 21 
 content-type: text/plain; charset=utf-8 
 date: Sun,22 Feb 2026 20:36:59 GMT 
 server: uvicorn 
 ```

## проверка hasattr(vacancy, field) перед setattr
- Что меняет: если DTO содержит лишние/опечатанные имена полей, они будут проигнорированы, а не попытаются создаться/установиться. 


## теперь в update_data попадут только поля, которые клиент реально изменили а не все подряд меняются на дефолтные
- data.model_dump(exclude_unset=True) вместо data.model_dump()


по итогу было 
```
async def update_vacancy(
    session: AsyncSession, vacancy: Vacancy, data: VacancyUpdate
) -> Vacancy:
    for field, value in data.model_dump().items():
        setattr(vacancy, field, value)
    await session.commit()
    await session.refresh(vacancy)
    return vacancy
```
стало 
```
async def update_vacancy(
    session: AsyncSession, vacancy: Vacancy, data: VacancyUpdate
    ) -> Vacancy:
    for field, value in data.model_dump(exclude_unset=True).items():
        if hasattr(vacancy, field):
            setattr(vacancy, field, value)
    await session.commit()
    await session.refresh(vacancy)
    return vacancy
```
ошибка 




![alt text](<images/errors/updatevakancy ошибка.jpg>)




исправлено




![alt text](<images/fix/updatevakancy исправлено.jpg>)

# делаем удаление созданого пользователя успешно


# в создании повторного не знаю ошибка это или или не ошибка, но точно же не статус 200... не буду засчитывать как ошибку
```
Code	Description	Links
201	
Successful Response

Media type

application/json
Controls Accept header.
Example Value
Schema
{
  "title": "string",
  "timetable_mode_name": "string",
  "tag_name": "string",
  "city_name": "string",
  "published_at": "2026-02-22T20:30:46.159Z",
  "is_remote_available": true,
  "is_hot": true,
  "external_id": 0,
  "id": 0,
  "created_at": "2026-02-22T20:30:46.159Z"
}
```
поскольку в списке не появляется, значит всё нормально наверное, работает не трогаю 