# 🚀 デプロイメント戦略規約

<module_info>
このモジュールは、デプロイメントの戦略と規約を定義します。
安全で効率的なデプロイメントにより、継続的デリバリーを実現します。
</module_info>

## 📐 基本規約

### デプロイメント戦略

```yaml
フェーズ別戦略:
  開発初期（モノリス）:
    - 単一アプリケーション
    - 従来型デプロイメント
    - 簡単なCI/CD
    
  成長期（ハイブリッド）:
    - コンテナ化
    - 段階的マイクロサービス化
    - Blue-Green デプロイ
    
  成熟期（マイクロサービス）:
    - Kubernetes オーケストレーション
    - Canary デプロイメント
    - 独立したデプロイサイクル
```

### 環境構成

```yaml
必須環境:
  development: ローカル開発
  staging: 本番相当のテスト
  production: 本番

推奨環境:
  preview: PR毎の動的環境
  load-test: 負荷テスト専用
```

## 🐳 コンテナ化戦略

### Dockerファイル規約

```dockerfile
# マルチステージビルド
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine AS runtime
WORKDIR /app

# セキュリティ強化
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nextjs -u 1001

COPY --from=builder /app/node_modules ./node_modules
COPY --chown=nextjs:nodejs . .

USER nextjs
EXPOSE 3000

# ヘルスチェック
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

CMD ["node", "server.js"]
```

### イメージ管理

```yaml
タグ戦略:
  latest: 最新の安定版
  {version}: セマンティックバージョン
  {git-sha}: コミットハッシュ
  {branch-name}: ブランチ別

レジストリ:
  開発: Docker Hub / GitHub Container Registry
  本番: プライベートレジストリ（ECR、ACR等）
```

## ⚙️ CI/CDパイプライン

### GitHub Actions実装例

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test -- --coverage
      
      - name: Run linting
        run: npm run lint
      
      - name: Run type check
        run: npm run type-check

  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Build Docker image
        run: |
          docker build -t myapp:${{ github.sha }} .
          docker tag myapp:${{ github.sha }} myapp:latest
      
      - name: Push to registry
        run: |
          echo ${{ secrets.REGISTRY_PASSWORD }} | docker login -u ${{ secrets.REGISTRY_USERNAME }} --password-stdin
          docker push myapp:${{ github.sha }}
          docker push myapp:latest

  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    environment: staging
    
    steps:
      - name: Deploy to staging
        run: |
          # Kubernetes deployment
          kubectl set image deployment/myapp myapp=myapp:${{ github.sha }}
          kubectl rollout status deployment/myapp
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=staging

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Deploy to production
        run: |
          # Blue-Green deployment
          ./scripts/blue-green-deploy.sh myapp:${{ github.sha }}
```

## 🔄 デプロイメントパターン

### Blue-Green デプロイメント

```bash
#!/bin/bash
# blue-green-deploy.sh

NEW_IMAGE=$1
CURRENT_ENV=$(kubectl get service myapp -o jsonpath='{.spec.selector.version}')
NEW_ENV=$([ "$CURRENT_ENV" = "blue" ] && echo "green" || echo "blue")

echo "Current environment: $CURRENT_ENV"
echo "Deploying to: $NEW_ENV"

# 新環境にデプロイ
kubectl set image deployment/myapp-$NEW_ENV myapp=$NEW_IMAGE
kubectl rollout status deployment/myapp-$NEW_ENV --timeout=300s

# ヘルスチェック
if curl -f http://myapp-$NEW_ENV.internal/health; then
  echo "Health check passed, switching traffic"
  
  # トラフィック切り替え
  kubectl patch service myapp -p '{"spec":{"selector":{"version":"'$NEW_ENV'"}}}'
  
  echo "Deployment successful!"
else
  echo "Health check failed, rolling back"
  exit 1
fi
```

### Canary デプロイメント

```yaml
# canary-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-canary
spec:
  replicas: 1  # 10%のトラフィック
  selector:
    matchLabels:
      app: myapp
      version: canary
  template:
    metadata:
      labels:
        app: myapp
        version: canary
    spec:
      containers:
      - name: myapp
        image: myapp:new-version

---
# Istio VirtualService for traffic splitting
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp
spec:
  http:
  - match:
    - headers:
        canary:
          exact: "true"
    route:
    - destination:
        host: myapp
        subset: canary
      weight: 100
  - route:
    - destination:
        host: myapp
        subset: stable
      weight: 90
    - destination:
        host: myapp
        subset: canary
      weight: 10
```

## ☸️ Kubernetes設定

### 基本Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:latest
        ports:
        - containerPort: 3000
        
        # リソース制限
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        
        # ヘルスチェック
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
        
        # 環境変数
        env:
        - name: NODE_ENV
          value: "production"
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: database-url

---
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 3000
  type: ClusterIP
```

## 🔐 シークレット管理

### 外部シークレット管理

```yaml
# External Secrets Operator
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: vault-backend
spec:
  provider:
    vault:
      server: "https://vault.example.com"
      path: "secret"
      version: "v2"
      auth:
        kubernetes:
          mountPath: "kubernetes"
          role: "myapp"

---
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: app-secrets
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: vault-backend
    kind: SecretStore
  target:
    name: app-secrets
    creationPolicy: Owner
  data:
  - secretKey: database-url
    remoteRef:
      key: myapp/config
      property: database_url
```

## 📊 デプロイメント監視

### デプロイメントメトリクス

```yaml
必須メトリクス:
  - デプロイ成功率
  - デプロイ所要時間
  - ロールバック頻度
  - MTTR（平均復旧時間）

SLI設定:
  デプロイ成功率: 99%以上
  デプロイ時間: 5分以内
  自動ロールバック: エラー率5%で発動
```

## 🚨 障害対応

### 自動ロールバック

```bash
#!/bin/bash
# auto-rollback.sh

DEPLOYMENT=$1
NAMESPACE=${2:-default}
ERROR_THRESHOLD=5  # 5%

# 現在のエラー率を取得
ERROR_RATE=$(kubectl logs -l app=$DEPLOYMENT --tail=1000 | grep ERROR | wc -l)
TOTAL_LOGS=$(kubectl logs -l app=$DEPLOYMENT --tail=1000 | wc -l)
CURRENT_ERROR_RATE=$((ERROR_RATE * 100 / TOTAL_LOGS))

if [ $CURRENT_ERROR_RATE -gt $ERROR_THRESHOLD ]; then
  echo "Error rate $CURRENT_ERROR_RATE% exceeds threshold $ERROR_THRESHOLD%"
  echo "Initiating rollback..."
  
  kubectl rollout undo deployment/$DEPLOYMENT -n $NAMESPACE
  kubectl rollout status deployment/$DEPLOYMENT -n $NAMESPACE
  
  # アラート送信
  curl -X POST $SLACK_WEBHOOK -d '{"text":"Auto-rollback triggered for '$DEPLOYMENT'"}'
fi
```

## 🧪 テスト自動化

```yaml
デプロイメントテスト:
  smoke-test: 基本機能の動作確認
  health-check: ヘルスエンドポイントの確認
  integration-test: 外部依存関係の確認
  
実行タイミング:
  post-deploy: デプロイ直後
  scheduled: 定期実行
  pre-rollback: ロールバック前
```

## 📋 チェックリスト

```yaml
デプロイ前:
  - [ ] 全テストが成功
  - [ ] セキュリティスキャン完了
  - [ ] パフォーマンステスト完了
  - [ ] 設定ファイルの確認

デプロイ後:
  - [ ] ヘルスチェック確認
  - [ ] メトリクス監視
  - [ ] ログエラー確認
  - [ ] ユーザー影響の監視
```