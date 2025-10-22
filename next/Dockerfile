# ビルド
FROM node:20-alpine AS build
WORKDIR /app

# ビルド時の統計データ自動送信の無効化
ENV NEXT_TELEMETRY_DISABLED=1
COPY package*.json ./

# バージョンを固定してインストール
RUN npm ci

COPY . .
RUN npm run build

# 動作用
FROM node:20-alpine AS run
WORKDIR /app
ENV NODE_ENV=production \
    NEXT_TELEMETRY_DISABLED=1 \
    PORT=3000 \
    HOSTNAME=0.0.0.0

# ビルド時に必要なものだけ出してくれるらしい (standalone) 
COPY --from=build /app/.next/standalone ./
COPY --from=build /app/.next/static ./.next/static
COPY --from=build /app/public ./public

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD node -e "fetch('http://localhost:3000/health_next').then(r=>r.ok?process.exit(0):process.exit(1)).catch(()=>process.exit(1))"

# ビルド時に生成される本番環境起動用スクリプト
CMD ["node", "server.js"]
