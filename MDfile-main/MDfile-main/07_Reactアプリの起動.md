# Reactアプリ(Docker)起動

npmでの起動:

```
npm start
```

# Docker Composeでの起動

```
# コンテナ作成&起動
docker-compose up --build

# 作成されたコンテナ起動
docker-compose up
```

# FastAPI起動
```
uvicorn main:app --reload
```
