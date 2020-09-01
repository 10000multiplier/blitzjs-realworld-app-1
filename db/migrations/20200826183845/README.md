# Migration `20200826183845`

This migration has been generated by Alan at 8/26/2020, 8:38:45 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
PRAGMA foreign_keys=OFF;

CREATE TABLE "new_Session" (
"id" TEXT NOT NULL,
"createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
"updatedAt" DATETIME NOT NULL,
"expiresAt" DATETIME ,
"handle" TEXT NOT NULL,
"userId" TEXT ,
"hashedSessionToken" TEXT ,
"antiCSRFToken" TEXT ,
"publicData" TEXT ,
"privateData" TEXT ,
PRIMARY KEY ("id"),
FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

INSERT INTO "new_Session" ("id", "createdAt", "updatedAt", "expiresAt", "handle", "userId", "hashedSessionToken", "antiCSRFToken", "publicData", "privateData") SELECT "id", "createdAt", "updatedAt", "expiresAt", "handle", "userId", "hashedSessionToken", "antiCSRFToken", "publicData", "privateData" FROM "Session"

PRAGMA foreign_keys=off;
DROP TABLE "Session";;
PRAGMA foreign_keys=on

ALTER TABLE "new_Session" RENAME TO "Session";

CREATE UNIQUE INDEX "Session.handle_unique" ON "Session"("handle")

CREATE TABLE "new_Tag" (
"id" TEXT NOT NULL,
"createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
"updatedAt" DATETIME NOT NULL,
"name" TEXT NOT NULL,
PRIMARY KEY ("id"))

INSERT INTO "new_Tag" ("id", "createdAt", "updatedAt", "name") SELECT "id", "createdAt", "updatedAt", "name" FROM "Tag"

PRAGMA foreign_keys=off;
DROP TABLE "Tag";;
PRAGMA foreign_keys=on

ALTER TABLE "new_Tag" RENAME TO "Tag";

CREATE UNIQUE INDEX "Tag.name_unique" ON "Tag"("name")

CREATE TABLE "new__PostToTag" (
"A" TEXT NOT NULL,
"B" TEXT NOT NULL,
FOREIGN KEY ("A") REFERENCES "Post"("id") ON DELETE CASCADE ON UPDATE CASCADE,

FOREIGN KEY ("B") REFERENCES "Tag"("id") ON DELETE CASCADE ON UPDATE CASCADE
)

INSERT INTO "new__PostToTag" ("A", "B") SELECT "A", "B" FROM "_PostToTag"

PRAGMA foreign_keys=off;
DROP TABLE "_PostToTag";;
PRAGMA foreign_keys=on

ALTER TABLE "new__PostToTag" RENAME TO "_PostToTag";

CREATE UNIQUE INDEX "_PostToTag_AB_unique" ON "_PostToTag"("A","B")

CREATE  INDEX "_PostToTag_B_index" ON "_PostToTag"("B")

CREATE TABLE "new__UserFollows" (
"A" TEXT NOT NULL,
"B" TEXT NOT NULL,
FOREIGN KEY ("A") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE,

FOREIGN KEY ("B") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE
)

INSERT INTO "new__UserFollows" ("A", "B") SELECT "A", "B" FROM "_UserFollows"

PRAGMA foreign_keys=off;
DROP TABLE "_UserFollows";;
PRAGMA foreign_keys=on

ALTER TABLE "new__UserFollows" RENAME TO "_UserFollows";

CREATE UNIQUE INDEX "_UserFollows_AB_unique" ON "_UserFollows"("A","B")

CREATE  INDEX "_UserFollows_B_index" ON "_UserFollows"("B")

CREATE TABLE "new__Favorites" (
"A" TEXT NOT NULL,
"B" TEXT NOT NULL,
FOREIGN KEY ("A") REFERENCES "Post"("id") ON DELETE CASCADE ON UPDATE CASCADE,

FOREIGN KEY ("B") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE
)

INSERT INTO "new__Favorites" ("A", "B") SELECT "A", "B" FROM "_Favorites"

PRAGMA foreign_keys=off;
DROP TABLE "_Favorites";;
PRAGMA foreign_keys=on

ALTER TABLE "new__Favorites" RENAME TO "_Favorites";

CREATE UNIQUE INDEX "_Favorites_AB_unique" ON "_Favorites"("A","B")

CREATE  INDEX "_Favorites_B_index" ON "_Favorites"("B")

CREATE TABLE "new_Comment" (
"id" TEXT NOT NULL,
"createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
"updatedAt" DATETIME NOT NULL,
"content" TEXT NOT NULL,
"userId" TEXT ,
"postId" TEXT ,
PRIMARY KEY ("id"),
FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE SET NULL ON UPDATE CASCADE,

FOREIGN KEY ("postId") REFERENCES "Post"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

INSERT INTO "new_Comment" ("id", "createdAt", "updatedAt", "content", "userId", "postId") SELECT "id", "createdAt", "updatedAt", "content", "userId", "postId" FROM "Comment"

PRAGMA foreign_keys=off;
DROP TABLE "Comment";;
PRAGMA foreign_keys=on

ALTER TABLE "new_Comment" RENAME TO "Comment";

CREATE TABLE "new_User" (
"id" TEXT NOT NULL,
"createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
"updatedAt" DATETIME NOT NULL,
"name" TEXT ,
"email" TEXT NOT NULL,
"hashedPassword" TEXT ,
"role" TEXT NOT NULL DEFAULT 'user',
"bio" TEXT NOT NULL DEFAULT '',
PRIMARY KEY ("id"))

INSERT INTO "new_User" ("id", "createdAt", "updatedAt", "role", "name", "email", "hashedPassword", "bio") SELECT "id", "createdAt", "updatedAt", "role", "name", "email", "hashedPassword", "bio" FROM "User"

PRAGMA foreign_keys=off;
DROP TABLE "User";;
PRAGMA foreign_keys=on

ALTER TABLE "new_User" RENAME TO "User";

CREATE UNIQUE INDEX "User.email_unique" ON "User"("email")

CREATE TABLE "new_Post" (
"id" TEXT NOT NULL,
"createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
"updatedAt" DATETIME NOT NULL,
"title" TEXT NOT NULL,
"intro" TEXT NOT NULL,
"content" TEXT NOT NULL,
"userId" TEXT ,
"status" TEXT NOT NULL DEFAULT 'draft',
PRIMARY KEY ("id"),
FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

INSERT INTO "new_Post" ("id", "createdAt", "updatedAt", "title", "intro", "content", "userId", "status") SELECT "id", "createdAt", "updatedAt", "title", "intro", "content", "userId", "status" FROM "Post"

PRAGMA foreign_keys=off;
DROP TABLE "Post";;
PRAGMA foreign_keys=on

ALTER TABLE "new_Post" RENAME TO "Post";

PRAGMA foreign_key_check;

PRAGMA foreign_keys=ON;
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration 20200824205522..20200826183845
--- datamodel.dml
+++ datamodel.dml
@@ -2,16 +2,16 @@
 // learn more about it in the docs: https://pris.ly/d/prisma-schema
 datasource sqlite {
   provider = "sqlite"
-  url = "***"
+  url = "***"
 }
 // SQLite is easy to start with, but if you use Postgres in production
 // you should also use it in development with the following:
 //datasource postgresql {
 //  provider = "postgresql"
-//  url = "***"
+//  url = "***"
 //}
 generator client {
   provider        = "prisma-client-js"
@@ -20,9 +20,9 @@
 // --------------------------------------
 model User {
-  id             Int       @default(autoincrement()) @id
+  id             String    @id @default(uuid())
   createdAt      DateTime  @default(now())
   updatedAt      DateTime  @updatedAt
   name           String?
   email          String    @unique
@@ -37,49 +37,49 @@
   bio            String    @default("")
 }
 model Session {
-  id                 Int       @default(autoincrement()) @id
+  id                 String    @id @default(uuid())
   createdAt          DateTime  @default(now())
   updatedAt          DateTime  @updatedAt
   expiresAt          DateTime?
   handle             String    @unique
   user               User?     @relation(fields: [userId], references: [id])
-  userId             Int?
+  userId             String?
   hashedSessionToken String?
   antiCSRFToken      String?
   publicData         String?
   privateData        String?
 }
 model Post {
-  id          Int       @default(autoincrement()) @id
+  id          String    @id @default(uuid())
   createdAt   DateTime  @default(now())
   updatedAt   DateTime  @updatedAt
   title       String
   intro       String
   content     String
   tags        Tag[]     @relation(references: [id])
   User        User?     @relation(fields: [userId], references: [id])
-  userId      Int?
+  userId      String?
   favoritedBy User[]    @relation("Favorites", references: [id])
   comments    Comment[]
   status      String    @default("draft")
 }
 model Comment {
-  id        Int      @default(autoincrement()) @id
+  id        String   @id @default(uuid())
   createdAt DateTime @default(now())
   updatedAt DateTime @updatedAt
   content   String
   user      User?    @relation(fields: [userId], references: [id])
-  userId    Int?
+  userId    String?
   post      Post?    @relation(fields: [postId], references: [id])
-  postId    Int?
+  postId    String?
 }
 model Tag {
-  id        Int      @default(autoincrement()) @id
+  id        String   @id @default(uuid())
   createdAt DateTime @default(now())
   updatedAt DateTime @updatedAt
   name      String   @unique
   posts     Post[]   @relation(references: [id])
```

