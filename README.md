# Systemd 容器镜像

支持 systemd、Ansible 和 Ollama 的 Docker 容器镜像集合，基于 buildpack-deps 官方镜像构建。

## 支持的版本

本仓库提供多个 Linux 发行版的 systemd 容器镜像：

### Debian 系列

- Debian 11 Bullseye
- Debian 12 Bookworm

### Ubuntu LTS 系列

- Ubuntu 20.04 Focal Fossa
- Ubuntu 22.04 Jammy Jellyfish
- Ubuntu 24.04 Noble Numbat

## 镜像特性

所有镜像均包含以下组件：

- systemd 完整支持
- Ansible 最新版本
- Ollama 大语言模型运行环境
- Python3 和 pip
- 完整的开发工具链（来自 buildpack-deps）
- 常用工具：sudo、curl、wget、git 等

## 预配置用户

所有镜像预配置了以下用户：

- root 密码 123456789
- Administrator 密码 123456789，sudo 权限
- matrix0523 密码 123456789，sudo 权限，NOPASSWD
- ollama 系统用户

## 快速部署

### 使用 Docker Compose（推荐）

本仓库为每个镜像提供了 docker-compose 文件，方便快速部署。

**重要提示**：systemd 容器需要在后台运行，请使用 `-d` 参数。

```bash
# Debian 12 Bookworm（后台运行）
docker compose -f docker-compose.debian-bookworm.yml up -d

# Debian 11 Bullseye
docker compose -f docker-compose.debian-bullseye.yml up -d

# Ubuntu 24.04 Noble
docker compose -f docker-compose.ubuntu-noble.yml up -d

# Ubuntu 22.04 Jammy
docker compose -f docker-compose.ubuntu-jammy.yml up -d

# Ubuntu 20.04 Focal
docker compose -f docker-compose.ubuntu-focal.yml up -d
```

**常用命令：**

```bash
# 查看容器状态
docker compose -f docker-compose.debian-bookworm.yml ps

# 查看容器日志
docker compose -f docker-compose.debian-bookworm.yml logs -f

# 停止并删除容器
docker compose -f docker-compose.debian-bookworm.yml down

# 进入容器
docker exec -it debian-bookworm-systemd /bin/bash
```

可用的 docker-compose 文件：

- `docker-compose.debian-bookworm.yml` - Debian 12 Bookworm
- `docker-compose.debian-bullseye.yml` - Debian 11 Bullseye
- `docker-compose.ubuntu-noble.yml` - Ubuntu 24.04 Noble
- `docker-compose.ubuntu-jammy.yml` - Ubuntu 22.04 Jammy
- `docker-compose.ubuntu-focal.yml` - Ubuntu 20.04 Focal
- `docker-compose.multi.yml` - 多容器示例（同时运行所有版本）

### 多容器部署

如需同时运行多个版本的容器进行测试或开发：

```bash
# 后台运行所有容器
docker compose -f docker-compose.multi.yml up -d

# 查看所有容器状态
docker compose -f docker-compose.multi.yml ps

# 停止所有容器
docker compose -f docker-compose.multi.yml down
```

这将启动所有 5 个版本的容器，并通过不同的 SSH 端口访问：
- Debian 12 Bookworm: 端口 2201
- Debian 11 Bullseye: 端口 2202
- Ubuntu 24.04 Noble: 端口 2203
- Ubuntu 22.04 Jammy: 端口 2204
- Ubuntu 20.04 Focal: 端口 2205

## 构建方法

选择要构建的版本，使用对应文件夹中的 Dockerfile：

```bash
# Debian 12 Bookworm
docker build -t debian-bookworm-systemd -f debian-bookworm/.devcontainer/Dockerfile .

# Ubuntu 24.04 Noble
docker build -t ubuntu-noble-systemd -f ubuntu-noble/.devcontainer/Dockerfile .
```

可用的 Dockerfile 位置：

- `debian-bookworm/.devcontainer/Dockerfile`
- `debian-bullseye/.devcontainer/Dockerfile`
- `ubuntu-noble/.devcontainer/Dockerfile`
- `ubuntu-jammy/.devcontainer/Dockerfile`
- `ubuntu-focal/.devcontainer/Dockerfile`

## 使用方法

### 从 Docker Hub 拉取镜像

```bash
# AMD64 架构
docker pull sunshinecloud007/sunshinecloud-systemd-devcontainer:debian-bookworm
docker pull sunshinecloud007/sunshinecloud-systemd-devcontainer:ubuntu-noble

# ARM64 架构
docker pull sunshinecloud007/sunshinecloud-systemd-devcontainer:debian-bookworm
docker pull sunshinecloud007/sunshinecloud-systemd-devcontainer:ubuntu-noble-arm64
```

可用的镜像标签：

**AMD64 架构：**
- `debian-bullseye`, `debian-bullseye-latest`
- `debian-bookworm`, `debian-bookworm-latest`
- `ubuntu-focal`, `ubuntu-focal-latest`
- `ubuntu-jammy`, `ubuntu-jammy-latest`
- `ubuntu-noble`, `ubuntu-noble-latest`

**ARM64 架构：**
- `debian-bullseye-arm64`, `debian-bullseye-latest-arm64`
- `debian-bookworm-arm64`, `debian-bookworm-latest-arm64`
- `ubuntu-focal-arm64`, `ubuntu-focal-latest-arm64`
- `ubuntu-jammy-arm64`, `ubuntu-jammy-latest-arm64`
- `ubuntu-noble-arm64`, `ubuntu-noble-latest-arm64`

### 运行容器

```bash
docker run --detach --privileged \
  --volume=/sys/fs/cgroup:/sys/fs/cgroup:rw \
  --cgroupns=host \
  --name my-systemd-container \
  sunshinecloud007/sunshinecloud-systemd-devcontainer:debian-bookworm
```

### 进入容器

```bash
docker exec -it my-systemd-container /bin/bash
```

### 使用 Ansible

```bash
docker exec --tty my-systemd-container env TERM=xterm ansible --version
docker exec --tty my-systemd-container env TERM=xterm ansible-playbook /path/to/playbook.yml
```

### 使用 Ollama

```bash
docker exec --tty my-systemd-container ollama run llama2
```

## 自动化构建

本仓库使用 GitHub Actions 自动构建和发布镜像：

- 每周一定时构建所有版本
- 推送到 master 分支时自动构建
- 支持手动触发构建
- 多架构支持：linux/amd64 和 linux/arm64
- 自动镜像压缩以减小体积

### 构建流程

1. **构建阶段**：使用 devcontainers/ci 构建多架构基础镜像（`-base` 标签）
2. **压缩阶段**：
   - AMD64：在 `ubuntu-latest` runner 上压缩
   - ARM64：在 `ubuntu-24.04-arm` runner 上压缩
3. **发布**：推送至 Docker Hub

## 注意事项

本镜像包含预配置的用户和密码，仅用于测试和开发环境。

生产环境使用前请务必：

1. 修改所有用户密码
2. 删除不需要的用户
3. 配置适当的安全策略
4. 使用防火墙限制访问

## 维护者

SunshineCloudTech
