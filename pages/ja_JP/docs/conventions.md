---
title: 規則
layout: page
---

## gorm.Model

`gorm.Model`は`ID`, `CreatedAt`, `UpdatedAt`, `DeletedAt`というフィールドを持つ、GoのStructです。

あなたのモデルに組み込んで使っても良いですし、組み込まずに独自のモデルを使っても構いません。

```go
// gorm.Modelの定義
type Model struct {
  ID        uint `gorm:"primary_key"`
  CreatedAt time.Time
  UpdatedAt time.Time
  DeletedAt *time.Time
}

// `ID`, `CreatedAt`, `UpdatedAt`, `DeletedAt`フィールドを`User`モデルに注入します
type User struct {
  gorm.Model
  Name string
}

// gorm.Model無しにモデルを宣言します
type User struct {
  ID   int
  Name string
}
```

## 主キーとしての`ID`

GORMはデフォルトでは`ID`という名前のフィールドをテーブルの主キーとして扱います。

```go
type User struct {
  ID   string // `ID`という名前のフィールドはデフォルトで主キーとして扱われます
  Name string
}

// `AnimalID`フィールドを主キーに設定します
type Animal struct {
  AnimalID int64 `gorm:"primary_key"`
  Name     string
  Age      int64
}
```

## 複数形のテーブル名

テーブル名は、Struct名の複数形が使われます。

```go
type User struct {} // default table name is `users`

// Set User's table name to be `profiles`
func (User) TableName() string {
  return "profiles"
}

func (u User) TableName() string {
  if u.Role == "admin" {
    return "admin_users"
  } else {
    return "users"
  }
}

// Disable table name's pluralization, if set to true, `User`'s table name will be `user`
db.SingularTable(true)
```

### テーブル名の指定

```go
// User構造体の定義を使って`deleted_users`テーブルを作成します
db.Table("deleted_users").CreateTable(&User{})

var deleted_users []User
db.Table("deleted_users").Find(&deleted_users)
//// SELECT * FROM deleted_users;

db.Table("deleted_users").Where("name = ?", "jinzhu").Delete()
//// DELETE FROM deleted_users WHERE name = 'jinzhu';
```

### デフォルトのテーブル名の変更

`DefaultTableNameHandler`を定義すると、デフォルトのテーブル名の生成ルールを変更できます。

```go
gorm.DefaultTableNameHandler = func (db *gorm.DB, defaultTableName string) string  {
  return "prefix_" + defaultTableName;
}
```

## スネークケースのカラム名

カラム名はスネークケース化されたフィールド名になります。

```go
type User struct {
  ID        uint      // column name is `id`
  Name      string    // column name is `name`
  Birthday  time.Time // column name is `birthday`
  CreatedAt time.Time // column name is `created_at`
}

// Overriding Column Name
type Animal struct {
  AnimalId    int64     `gorm:"column:beast_id"`         // set column name to `beast_id`
  Birthday    time.Time `gorm:"column:day_of_the_beast"` // set column name to `day_of_the_beast`
  Age         int64     `gorm:"column:age_of_the_beast"` // set column name to `age_of_the_beast`
}
```

## タイムスタンプの追跡

### CreatedAt

`CreatedAt`フィールドの持つモデルでは、レコードの初回生成時に現在時刻が設定されます。

```go
db.Create(&user) // `CreatedAt`には現在時刻が設定されます

// 値を変更するには`Update`を使います
db.Model(&user).Update("CreatedAt", time.Now())
```

### UpdatedAt

`UpdatedAt`フィールドを持つモデルでは、レコード保存時に現在時刻が設定されます

```go
db.Save(&user) // `UpdatedAt`に現在時刻を設定します

db.Model(&user).Update("name", "jinzhu") // `UpdatedAt`に現在時刻を設定します
```

### DeletedAt

モデルに`DeletedAt`フィールドが存在する場合、`Delete`が呼ばれても実際にはデータベースからデータは削除されません。代わりに`DeletedAt`に`Delete`が呼ばれた時の時刻がセットされます。 詳しくは[Soft Delete](/docs/delete.html#Soft-Delete)を参照してください。