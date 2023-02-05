---
title: Next-generation ORM：Prisma
date: "2023-01-01T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/humane-typography-in-the-digital-age"
category: "Typography"
tags:
  - "ORM"
  - "Prisma"
  - "Web Development"
description: "經常寫 Node 應用程式的人通常免不了要和 ORM 打交道，畢竟寫原生 SQL 對於大部分的人會比較複雜也或許情境不需要用到這樣的原生操作。ORM 的便利性使得很多情況下我們能直觀而快速地取得資料庫的資料。"
---

- [Next-generation ORM：Prisma](#next-generation-orm-prisma)
- [What is Prisma?](#what-is-prisma)
- [Why Prisma ?](#why-prisma)

## Next-generation ORM：Prisma

經常寫 Node 應用程式的人通常免不了要和 ORM 打交道，畢竟寫原生 SQL 對於大部分的人會比較複雜也或許情境不需要用到這樣的原生操作。ORM 的便利性使得很多情況下我們能直觀而快速地取得資料庫的資料。

## What is Prisma?

Prisma 是開源程式，是在 NodeJS 社群中的 ORM，老牌的 ORM 像是 TypeORM、MikroORM 或是 MongoDB 專用的 ORM mongoose。

Web 的應用開發過程中，需要花費大量時間與關連式資料庫操作，需要花很多時來調適 SQL 查詢語句，Prisma 則是提供一套簡潔的 API，可以更加方便操作資料庫，不僅提供了操作資料庫的 API，還可以依照需求，透過工具組合來建構 Rest API 或是 GraphQL，支援了多種資料庫串接，包含 PostgreSQL, MySQL, SQLite, MongoDB, CrockoachDB 與 MSSQL 。

包含了以下

- **Prisma Client：Auto-generated and type-safe query builder for Node.js & TypeScript**
- **Prisma Migrate：Migration system**
- **Prisma Studio： 資料庫的 GUI 介面**

## Why Prisma?

1. **Thinking in objects instead of mapping relational data**
2. **Queries not classes to avoid complex model objects**
3. **Single source of truth** for database and application models
4. **Healthy constraints** that prevent common pitfalls and antipatterns
5. **An abstraction that makes the right thing easy**
6. **Type-safe database queries** that can be validated at compile time
7. **Auto-completion in code editors** instead of needing to look up documentation

## ORM( Object Relational Mapping)

資料庫與程式之間做了一層對應，與資料庫的語法都是使用 ORM 的語法，ORM 換轉換成 SQL 去執行，所以在操作上並非直接去寫原始的 SQL。

**原始 SQL：完全控制，生產率低**

可以完全對資料庫發送純 SQL 字符串，才操作資料庫，但直接下 SQL 指令很麻煩，並且會帶來大量的開銷，所以對程式的生產力降低。

**SQL 查詢構建器(SQL query builders)：高控制，中等生產力**

保留高控制並提供更高生產力的常見解決方案是使用 SQL 查詢構建器，像是 knex.js，仍然需要根據 SQL 來存取資料，還是會有將關係資料轉換成物件對象的成本。

![](/media/prisma6.png)

**ORM：更少的控制，更高的生產力**

ORM 存在主要是為了替程式設計師架起資料庫中間的橋樑，可以達到更有效的訪問。

![](/media/prisma5.png)

### 特點

1. Schema 定義，我們所認識的 ORM 都是使用 JS/TS 進行定義的，而 Prisma 則使用`.prisma` 文件來撰寫表的結構，以下用 Sequelize 與 Prisma model 的示例

**Sequelize model**

```js
class User extends Model {}
User.init(
  {
    user_id: {
      type: Sequelize.INTEGER,
      primaryKey: true,
      autoIncrement: true,
    },
    name: Sequelize.STRING(255),
    email: {
      type: Sequelize.STRING(255),
      unique: true,
    },
    isAdmin: Sequelize.BOOLEAN,
  },
  { sequelize, modelName: "user" },
);
```

**Prisma Schema model**

簡單的 prisma 定義如下

```js
model User {
  user_id Int     @id @default(autoincrement())
  name    String?
  email   String  @unique
  isAdmin Boolean @default(false)
}
```

2. Typescript 類型定義更加全面，包含查詢參數、操作符參數、級聯參數、返回結果等。
3. 更加全面的操作符 contains、startsWith、endsWith
4. 全面的工具涵蓋資料庫的流程
   1. querying （[Prisma Client](https://www.prisma.io/docs/concepts/components/prisma-client)）
   2. data modeling ([Prisma schema](https://www.prisma.io/docs/concepts/components/prisma-schema))
   3. migrations ([Prisma Migrate](https://www.prisma.io/docs/concepts/components/prisma-migrate))
   4. prototyping via prisma db push
   5. seeding via prisma db seed
   6. visual viewing and editing （with [Prisma Studio](https://prisma.io/studio)）

---

### Quick start

Node.js version 14.17.0 or higher

Access to a MongoDB 4.2+

### Set up package environment

```
# 初始化專案
npm init -y
npm install prisma typescript ts-node @types/node --save-dev

# Prisma CLI
npx prisma
npx prisma init
```

`tsconfig.json`

```json
{
  "compilerOptions": {
    "sourceMap": true,
    "outDir": "dist",
    "strict": true,
    "lib": ["esnext"],
    "esModuleInterop": true
  }
}
```

### Connect to DB

在`.env`檔案裡修改連接資訊

```
DATABASE_URL = 'mongodb://localhost:27017/prisma';
```

### Set up schema

```js
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mongodb"
  url      = env("DATABASE_URL")
}

model Post {
  id       String    @id @default(auto()) @map("_id") @db.ObjectId
  slug     String    @unique
  title    String
  body     String
  comments Comment[]
  author   User      @relation(fields: [authorId], references: [id])
  authorId String    @db.ObjectId
}

model Comment {
  id      String @id @default(auto()) @map("_id") @db.ObjectId
  post    Post   @relation(fields: [postId], references: [id])
  postId  String @db.ObjectId
  comment String
}

model User {
  id    String  @id @default(auto()) @map("_id") @db.ObjectId
  email String  @unique
  name  String?
  posts Post[]
}
```

![](/media/prisma4.png)

### Install Prisma Client

用來操作資料庫的 prisma 套件

```
npm install @prisma/client
```

install command automatically invokes **`prisma generate`**

如果有更改表的結構，需要先執行`prisma generate` 讓 Prisma Client 可以對應到 model。

![](/media/prisma3.png)

### Create and query users

```js
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

async function main() {
  await prisma.$connect();
  await prisma.user.create({
    data: {
      name: "Rich",
      email: "hello@prisma.com",
      posts: {
        create: {
          title: "My first post",
          body: "Lots of really interesting stuff",
          slug: "my-first-post",
        },
      },
    },
  });
  const allUsers = await prisma.user.findMany();
  console.log(allUsers);
}

main()
  .then(async () => {
    await prisma.$disconnect();
  })
  .catch(async (e) => {
    console.error(e);
    await prisma.$disconnect();
    process.exit(1);
  });
```

### Prisma Migration

Migration 是用來描述資料庫結構是什麼樣子，會隨著專案開發中逐漸增加，使用 Migration 進入版本控制後，整個資料庫的設計過程一目瞭然，方便一起協作。

1. 建立好資料結構後

```js
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlserver"
  url      = env("DATABASE_URL")
}

model Post {
  id        Int      @id @default(autoincrement())
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  title     String   @db.VarChar(255)
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
}

model Profile {
  id     Int     @id @default(autoincrement())
  bio    String?
  user   User    @relation(fields: [userId], references: [id])
  userId Int     @unique
}

model User {
  id      Int      @id @default(autoincrement())
  email   String   @unique
  name    String?
  posts   Post[]
  profile Profile?
}
```

2. 執行第一次的 migration

```
prisma migrate dev --name init
```

3. 結果顯示

```
Applying migration `20220803160039_init`

The following migration(s) have been created and applied from new schema changes:

migrations/
  └─ 20220803160039_init/
    └─ migration.sql
```

4. 新增 User 表的一個欄位

```js
model User {
  id       Int    @id @default(autoincrement())
  jobTitle String
  name     String
  posts    Post[]
}
```

5. 執行第二次的 migration

```js
prisma migrate dev --name added_job_title
```

```js
Applying migration `20220803160346_added_job_title`

The following migration(s) have been created and applied from new schema changes:

migrations/
  └─ 20220803160346_added_job_title/
    └─ migration.sql

Your database is now in sync with your schema.
```

DB 會有個表來呈現並檢查 migration 的過程`_prisma_migrations` 見下圖

![](/media/prisma2.png)

### Prisma Studio

Prisma 有提供可以查看 DB 結構的 GUI 介面，直接執行以下指令。

```js
npx prisma studio
```

![](/media/prisma1.png)

### 參考資料

[Prisma 官網](https://www.prisma.io/)

[MongoDB database connector](https://www.prisma.io/docs/concepts/database-connectors/mongodb#error-transactions-are-not-supported-by-this-deployment)

[Schema-driven development in 2021 - 99designs](https://99designs.com/blog/engineering/schema-driven-development/)

[What is Prisma and Why Do We Need Another ORM?](https://formidable.com/blog/2021/prisma-orm/)

[Code-first vs. schema-first development in GraphQL - LogRocket Blog](https://blog.logrocket.com/code-first-vs-schema-first-development-graphql/)
