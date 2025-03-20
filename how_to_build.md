```bash
# Step 1: 加载镜像
docker load -i naochat-image.tar.gz

# Step 2: 运行容器（保持原有配置）
docker run -d \
  -v /www/wwwroot/naochat/data:/app/backend/data \
  --name naochat \
  --network=host \
  naochat-image
```

```bash
# Step 1: 构建镜像（带优化参数）
docker build \
  --build-arg USE_CUDA=false \
  --build-arg USE_OLLAMA=false \
  --build-arg USE_EMBEDDING_MODEL="sentence-transformers/all-MiniLM-L6-v2" \
  --build-arg USE_RERANKING_MODEL="" \
  --no-cache \
  -t naochat-image .

# Step 2: 导出镜像为压缩包
docker save naochat-image | gzip > naochat-image.tar.gz
```

#### 1.2 分卷压缩（50M/卷）

```bash
split -b 50M naochat-image.tar.gz "naochat-image.tar.gz.part"
```

#### 1.3 合并与解压（接收端操作）

```
# 合并所有分卷（注意按字母顺序排列）
cat naochat-image.tar.gz.part* > naochat-image.tar.gz
```

```
# 上传所有.part*文件到服务器
scp naochat-image.tar.gz.part* root@43.134.78.15:/root/
```

---

```
docker run -d -p 9099:9099 --add-host=host.docker.internal:host-gateway -v pipelines:/app/pipelines --name pipelines --restart always ghcr.io/open-webui/pipelines:main

```

```
# 查看容器网络配置（在宿主机执行）
docker inspect -f '{{.HostConfig.NetworkMode}}' <容器ID>
```

