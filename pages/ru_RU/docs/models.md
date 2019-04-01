---
title: Объявление моделей
layout: страница
---
## Объявление моделей

Модели обычно являются обычными Golang структурами, основными типами Go или указателями. [`sql.Scanner`](https://golang.org/pkg/database/sql/#Scanner) и [`driver.Valuer`](https://golang.org/pkg/database/sql/driver/#Valuer) интерфейсы также поддерживаются.

Пример модели:

```go
type User struct {   
    gorm.Model   Name string   
    Age sql.NullInt64   
    Birthday *time.Time   
    Email string `gorm:"type:varchar(100);unique_index"`   
    Role string `gorm:"size:255"` // установим размер поля в 255 символов   
    MemberNumber *string `gorm:"unique;not null"` // сделаем поле уникальным и не null
    Num int `gorm:"AUTO_INCREMENT"` // установим число как автоинкремент
    Address string `gorm:"index:addr"` // сделаем индекс `addr` для поля
    IgnoreMe int `gorm:"-"` // игнорируем это поле
}
```

## Теги структуры

Теги необязательно используются при объявлении моделей. GORM поддерживает следующие теги:

### Поддерживаемые теги

| Тег             | Описание                                                                  |
| --------------- | ------------------------------------------------------------------------- |
| Column          | Указывает имя столбца                                                     |
| Type            | Укажите тип данных столбца                                                |
| Size            | Задает размер столбца, по умолчанию 255                                   |
| PRIMARY_KEY     | Определяет столбец как первичный ключ                                     |
| UNIQUE          | Указывает столбец как уникальный                                          |
| DEFAULT         | Задает значение столбца по умолчанию                                      |
| PRECISION       | Определяет точность столбца                                               |
| NOT NULL        | Определяет столбец как NOT NULL                                           |
| AUTO_INCREMENT  | Указывает столбец как автоинкрементный или нет                            |
| INDEX           | Создать индекс с именем или без него, то же имя создает составные индексы |
| UNIQUE_INDEX    | Как и `INDEX`, создает уникальный индекс                                  |
| EMBEDDED        | Установить структуру как встроенную                                       |
| EMBEDDED_PREFIX | Установить префикс для встроенной структуры                               |
| -               | Игнорировать эти поля                                                     |

### Теги структуры для ассоциаций

Check out the Associations section for details

| Тег                                | Описание                                          |
| ---------------------------------- | ------------------------------------------------- |
| MANY2MANY                          | Указывает имя таблицы объединения                 |
| FOREIGNKEY                         | Определяет внешний ключ                           |
| ASSOCIATION_FOREIGNKEY             | Определяет ассоциативный внешний ключ             |
| POLYMORPHIC                        | Определяет полиморфический тип                    |
| POLYMORPHIC_VALUE                  | Определяет полиморфическое значение               |
| JOINTABLE_FOREIGNKEY               | Определяет внешний ключ подключения               |
| ASSOCIATION_JOINTABLE_FOREIGNKEY | Определяет ассоциативный внешний ключ подключения |
| SAVE_ASSOCIATIONS                  | Автосохранение ассоциаций или нет                 |
| ASSOCIATION_AUTOUPDATE             | Автообновление ассоциаций или нет                 |
| ASSOCIATION_AUTOCREATE             | Автосоздание ассоциаций или нет                   |
| ASSOCIATION_SAVE_REFERENCE       | AutoSave associations reference or not            |
| PRELOAD                            | Предзагрузка ассоциаций или нет                   |