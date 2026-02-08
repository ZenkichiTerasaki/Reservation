FROM node:20

WORKDIR /app

# 依存関係だけ先にコピー（キャッシュ効かせる）
COPY package*.json ./
RUN npm install

# ソースコードをコピー
COPY . .

RUN npx prisma generate

# NestJS 起動
CMD ["npm", "run", "start:dev"]
