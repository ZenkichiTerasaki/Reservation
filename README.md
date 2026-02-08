# Reservation

## プロジェクトの生成

```
npx @nestjs/cli new backend
```

## プリズマの導入

```
cd backend
npm install prisma @prisma/client
npx prisma init
```

## dockerとの接続

次のファイルを作成

```
./backend
- Dockerfile //node.jsの実行環境をコピーして実行
- .dockerignore //コンテナに含まないファイルを記述(.envや含むと重いファイルを回避)

./
- docker-compose.yml //イメージの構成を記述
```

### コンテナ起動

```
cd root_d
docker-compose up -d --build
```

### マイグレーション実行
prismaの構成変更をデータベースに渡す

```
docker-compose exec api npx prisma migrate dev
```

### schema.prismaのテンプレート
```
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
}

/*以下テーブル定義*/
```

### src/prisma/ のテンプレート

#### prisma.module.ts

```
import { Global, Module } from '@nestjs/common';
import { PrismaService } from './prisma.service';

@Global()
@Module({
  providers: [PrismaService],
  exports: [PrismaService],
})
export class PrismaModule {}
```

#### prisma.service.ts
```
import { Injectable, OnModuleInit } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';
import { PrismaPg } from '@prisma/adapter-pg';

@Injectable()
export class PrismaService
  extends PrismaClient
  implements OnModuleInit {

  constructor() {
    const pool = new PrismaPg({ connectionString: process.env.DATABASE_URL! });
    super({ adapter: pool });
  }
  async onModuleInit() {
    await this.$connect();
  }
}
```