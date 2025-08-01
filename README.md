# gitlab-apiops-test

## 利用方法
### 事前準備
以下の変数を設定する。
- GitLab側で設定する変数
 - `CICD_KONG_ADMIN_TOKEN`: KongのAdminAPIにアクセスするためのトークン（RBAC有効時のみ）
- .gitlab-ci.ymlで設定する変数
 - `DECK_KONG_ADDR`: KongのAdminAPIのURL
 - `DECK_TLS_SKIP_VERIFY`: Kongが自己署名証明書を使用している場合はtrueに設定
 - `DECK_HEADERS`: AdminAPIに渡すヘッダ（特に要件なければ変更不要）
 - `WORKSPACE`: デプロイ先Workspace名

接続先となるサービス（httpbin）を展開する。
```bash
kubectl apply -f ./k8s
```

### 実行方法
openapi/httpbin.yamlを編集して、API仕様を変更するとパイプラインが実行される。
事前準備の環境変数が設定されている場合は以下のような感じでパイプラインが登録したKong設定が確認可能。
```sh
curl ${DECK_KONG_ADDR}/${WORKSPACE}/services -H "kong-admin-token:$CICD_KONG_ADMIN_TOKEN" | jq .
curl ${DECK_KONG_ADDR}/${WORKSPACE}/consumers -H "kong-admin-token:$CICD_KONG_ADMIN_TOKEN" | jq .
curl ${DECK_KONG_ADDR}/${WORKSPACE}/plugins -H "kong-admin-token:$CICD_KONG_ADMIN_TOKEN" | jq .
```
※ 必要に応じて:8001を指定する

またProxyに対してアクセスすることで適切にリクエストが転送されていることも確認可能。
```sh
curl -i <proxyのアドレス>/get
```

