# 部署指南

## 镜像信息

- **阿里云镜像地址**: `crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com`
- **命名空间**: `docker-zhengzhuang`
- **仓库名**: `new-api`
- **完整镜像地址**: `crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com/docker-zhengzhuang/new-api:latest`

## 一、登录阿里云镜像仓库

```bash
docker login --username=zhengzhuangpro crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com
```

输入密码完成登录。

## 二、构建镜像

```bash
# 在项目根目录执行
docker build -t crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com/docker-zhengzhuang/new-api:latest .
```

如果需要打版本标签：

```bash
docker build -t crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com/docker-zhengzhuang/new-api:v1.0.0 .
```

## 三、推送镜像

```bash
# 推送 latest
docker push crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com/docker-zhengzhuang/new-api:latest

# 推送指定版本
docker push crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com/docker-zhengzhuang/new-api:v1.0.0
```

## 四、服务器部署

### 方式一：docker run

```bash
# 登录镜像仓库
docker login --username=zhengzhuangpro crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com

# 拉取镜像
docker pull crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com/docker-zhengzhuang/new-api:latest

# 运行
docker run -d \
  --name new-api \
  --restart always \
  -p 3000:3000 \
  -v ./data:/data \
  -v ./logs:/app/logs \
  -e SQL_DSN=root:<YOUR_PASSWORD>@tcp(<YOUR_HOST>:3306)/new-api \
  -e REDIS_CONN_STRING=redis://:<YOUR_PASSWORD>@<YOUR_HOST>:6379 \
  -e TZ=Asia/Shanghai \
  crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com/docker-zhengzhuang/new-api:latest
```

### 方式二：docker compose

创建 `docker-compose.yml`：

```yaml
services:
  new-api:
    image: crpi-vstocxwxcg82n38f.cn-beijing.personal.cr.aliyuncs.com/docker-zhengzhuang/new-api:latest
    container_name: new-api
    restart: always
    command: --log-dir /app/logs
    ports:
      - "3000:3000"
    volumes:
      - ./data:/data
      - ./logs:/app/logs
    environment:
      - SQL_DSN=root:<YOUR_PASSWORD>@tcp(<YOUR_HOST>:3306)/new-api
      - REDIS_CONN_STRING=redis://:<YOUR_PASSWORD>@<YOUR_HOST>:6379
      - TZ=Asia/Shanghai
      - ERROR_LOG_ENABLED=true
      - BATCH_UPDATE_ENABLED=true
      - NODE_NAME=new-api-node-1
```

启动：

```bash
docker compose up -d
```

## 五、更新部署

```bash
docker compose pull
docker compose up -d
```

## 六、常用运维命令

```bash
# 查看日志
docker compose logs -f

# 重启
docker compose restart

# 停止并删除容器
docker compose down

# 进入容器
docker exec -it new-api sh

# 查看容器状态
docker ps
```
