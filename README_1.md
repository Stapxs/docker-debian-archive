# Debian Woody i386 镜像构建

在 GitHub Actions 上构建 Debian Woody (3.0) i386 架构的 Docker 镜像。

## 使用方法

### 1. 准备仓库

将以下文件推送到 GitHub 仓库：
```
.
├── .github/
│   └── workflows/
│       └── build-debian-woody.yml
├── build-archived-debian-image.sh
└── README.md
```

### 2. 配置 Docker Hub（可选）

如果需要推送到 Docker Hub，在仓库的 Settings > Secrets and variables > Actions 中添加：
- `DOCKER_USERNAME`: 你的 Docker Hub 用户名
- `DOCKER_PASSWORD`: 你的 Docker Hub 密码或 token

### 3. 触发构建

- **自动触发**: 推送到 main/master 分支
- **手动触发**: 在 Actions 标签页点击 "Build Debian Woody i386 Image" > "Run workflow"

### 4. 获取镜像

构建完成后有两种方式获取：

**方式 1: 下载 artifact**
1. 进入 Actions 标签页
2. 点击最近的 workflow 运行
3. 下载 `debian-woody-i386-image` artifact
4. 本地加载镜像：
   ```bash
   gunzip -c debian-woody-i386.tar.gz | docker load
   ```

**方式 2: 从 Docker Hub 拉取**（如果配置了推送）
```bash
docker pull madworx/debian-archive:woody
```

## 本地测试

```bash
# 构建 woody
./build-archived-debian-image.sh woody

# 运行容器
docker run -it madworx/debian-archive:woody /bin/sh

# 安装 gcc 2.95
docker run -it madworx/debian-archive:woody /bin/sh -c "
  echo 'deb http://archive.debian.org/debian woody main' > /etc/apt/sources.list
  apt-get update
  apt-get install -y gcc-2.95
  gcc-2.95 --version
"
```

## 支持的版本

修改 workflow 文件中的参数可以构建其他版本：
- `woody` - Debian 3.0 (2002)
- `sarge` - Debian 3.1 (2005)
- `etch` - Debian 4.0 (2007)
- `lenny` - Debian 5.0 (2009)

## 注意事项

- GitHub Actions 提供 x86_64 runner，可以原生构建 i386 镜像
- 每次构建约需 5-15 分钟
- Artifact 保留 7 天（可在 workflow 中调整）
- 免费账户每月有 2000 分钟的 Actions 额度
