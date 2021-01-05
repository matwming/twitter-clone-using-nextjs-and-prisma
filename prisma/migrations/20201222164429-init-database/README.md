# Migration `20201222164429-init-database`

This migration has been generated by Nirmalya Ghosh at 12/22/2020, 10:14:29 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TABLE "accounts" (
"id" SERIAL,
    "compound_id" TEXT NOT NULL,
    "user_id" INTEGER NOT NULL,
    "provider_type" TEXT NOT NULL,
    "provider_id" TEXT NOT NULL,
    "provider_account_id" TEXT NOT NULL,
    "refresh_token" TEXT,
    "access_token" TEXT,
    "access_token_expires" TIMESTAMP(3),
    "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,

    PRIMARY KEY ("id")
)

CREATE TABLE "sessions" (
"id" SERIAL,
    "user_id" INTEGER NOT NULL,
    "expires" TIMESTAMP(3) NOT NULL,
    "session_token" TEXT NOT NULL,
    "access_token" TEXT NOT NULL,
    "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,

    PRIMARY KEY ("id")
)

CREATE TABLE "users" (
"id" SERIAL,
    "name" TEXT,
    "email" TEXT,
    "email_verified" TIMESTAMP(3),
    "image" TEXT,
    "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,

    PRIMARY KEY ("id")
)

CREATE TABLE "verification_requests" (
"id" SERIAL,
    "identifier" TEXT NOT NULL,
    "token" TEXT NOT NULL,
    "expires" TIMESTAMP(3) NOT NULL,
    "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,

    PRIMARY KEY ("id")
)

CREATE TABLE "tweets" (
"id" SERIAL,
    "body" TEXT NOT NULL,
    "userId" INTEGER NOT NULL,
    "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,

    PRIMARY KEY ("id")
)

CREATE UNIQUE INDEX "accounts.compound_id_unique" ON "accounts"("compound_id")

CREATE INDEX "providerAccountId" ON "accounts"("provider_account_id")

CREATE INDEX "providerId" ON "accounts"("provider_id")

CREATE INDEX "userId" ON "accounts"("user_id")

CREATE UNIQUE INDEX "sessions.session_token_unique" ON "sessions"("session_token")

CREATE UNIQUE INDEX "sessions.access_token_unique" ON "sessions"("access_token")

CREATE UNIQUE INDEX "users.email_unique" ON "users"("email")

CREATE UNIQUE INDEX "verification_requests.token_unique" ON "verification_requests"("token")

ALTER TABLE "tweets" ADD FOREIGN KEY("userId")REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20201222164429-init-database
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,74 @@
+generator client {
+  provider = "prisma-client-js"
+}
+
+datasource db {
+  provider = "postgresql"
+  url = "***"
+}
+
+model Account {
+  id                 Int       @id @default(autoincrement())
+  compoundId         String    @unique @map("compound_id")
+  userId             Int       @map("user_id")
+  providerType       String    @map("provider_type")
+  providerId         String    @map("provider_id")
+  providerAccountId  String    @map("provider_account_id")
+  refreshToken       String?   @map("refresh_token")
+  accessToken        String?   @map("access_token")
+  accessTokenExpires DateTime? @map("access_token_expires")
+  createdAt          DateTime  @default(now()) @map("created_at")
+  updatedAt          DateTime  @default(now()) @map("updated_at")
+
+  @@index([providerAccountId], name: "providerAccountId")
+  @@index([providerId], name: "providerId")
+  @@index([userId], name: "userId")
+  @@map("accounts")
+}
+
+model Session {
+  id           Int      @id @default(autoincrement())
+  userId       Int      @map("user_id")
+  expires      DateTime
+  sessionToken String   @unique @map("session_token")
+  accessToken  String   @unique @map("access_token")
+  createdAt    DateTime @default(now()) @map("created_at")
+  updatedAt    DateTime @default(now()) @map("updated_at")
+
+  @@map("sessions")
+}
+
+model User {
+  id            Int       @id @default(autoincrement())
+  name          String?
+  email         String?   @unique
+  emailVerified DateTime? @map("email_verified")
+  image         String?
+  createdAt     DateTime  @default(now()) @map("created_at")
+  updatedAt     DateTime  @default(now()) @map("updated_at")
+  tweets        Tweet[]
+
+  @@map("users")
+}
+
+model VerificationRequest {
+  id         Int      @id @default(autoincrement())
+  identifier String
+  token      String   @unique
+  expires    DateTime
+  createdAt  DateTime @default(now()) @map("created_at")
+  updatedAt  DateTime @default(now()) @map("updated_at")
+
+  @@map("verification_requests")
+}
+
+model Tweet {
+  id        Int      @id @default(autoincrement())
+  body      String
+  userId    Int
+  createdAt DateTime @default(now()) @map("created_at")
+  updatedAt DateTime @default(now()) @map("updated_at")
+  author    User     @relation(fields: [userId], references: [id])
+
+  @@map("tweets")
+}
```

