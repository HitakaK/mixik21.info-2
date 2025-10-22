FROM nginx:1.27-alpine

# コンテナ内80番ポートへの疎通確認用
RUN apk add --no-cache curl

# ログを標準出力へ
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
 && ln -sf /dev/stderr /var/log/nginx/error.log

# 設定ファイル
COPY nginx.conf /etc/nginx/nginx.conf
COPY conf.d/ /etc/nginx/conf.d/
# COPY certs/ /etc/nginx/certs/

# 静的ファイルを直配信するなら、あとでボリュームやCOPYでここへ置く
# /var/www/site/public
# /var/www/site/_next/static

EXPOSE 80 
# EXPOSE 80 443
# 3回連続で/health_nginxへの疎通に失敗したらマーク
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD curl -fsS http://localhost/health_nginx || exit 1

# docker stopしても処理中の内容をやり遂げる
STOPSIGNAL SIGQUIT
