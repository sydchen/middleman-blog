---
title: Creating partial indexes in Rails 4
date: 2014-01-29 21:58
tags: rails,postgresql
---

[Partial Index](http://en.wikipedia.org/wiki/Partial_index)通常指的是說index可以只作用在表格中符合特定條件的rows。
維基百科上以PostgreSQL為例

```
CREATE INDEX partial_status ON txn_table (STATUS) 
WHERE STATUS IN ('A', 'P', 'W');
```

但MySQL InnoDB 的定義卻是

> An index that represents only part of a column value, typically the first N characters (the prefix) of a long VARCHAR value.

對於long VARCHAR column可對於**前N字元**設定index，所以也稱之為**index prefix**，好處是可以減少indexes空間，INSERT資料時也會快些。
至於有效的N值如何取得，可以參考[How to calculate cardinality](http://www.mysqldba.co.uk/articles/2008/02/26/how-to-calculate-cardinality/)這篇文章。

Rails 4 後`add_index` 開始支援partial index

以PostgreSQL為例，假設有個Book model

```
class CreateBooks < ActiveRecord::Migration
  def change
    create_table :books do |t|
      t.string :name
      t.string :author
      t.string :category
    end
  end
end
```

對於category = 'science'的row，欄位name和author建立partial index

```
add_index(:books, [:name, :author], unique: true, where: "category = 'sciency'")
```

`db/schema.rb`中產生如下的syntax

```
add_index "books", ["name", "author"], name: "index_books_on_name_and_author", unique: true, where: "((category)::text = 'science'::text)", using: :btree
```

在Rails console測試

```
irb(main):001:0> Book.create(name: 'xxx', author: 'yyy', category: 'science')
   (0.2ms)  BEGIN
  SQL (1.2ms)  INSERT INTO "books" ("author", "category", "name") VALUES ($1, $2, $3) RETURNING "id"  [["author", "yyy"], ["category", "science"], ["name", "xxx"]]
   (0.4ms)  COMMIT
=> #<Book id: 1, name: "xxx", author: "yyy", category: "science">
```

第二次就會丟出Exception

```
ActiveRecord::RecordNotUnique: PG::UniqueViolation: ERROR:  duplicate key value violates unique constraint "index_books_on_name_and_author$
DETAIL:  Key (name, author)=(xxx, yyy) already exists.
```
