# Systemd 容器镜像

支持 systemd、Ansible 和 Ollama 的 Docker 容器镜像集合，基于 buildpack-deps 官方镜像构建。

## 支持的版本

本仓库提供多个 Linux 发行版的 systemd 容器镜像：

### Debian 系列

- Debian 10 Buster
- Debian 11 Bullseye
- Debian 12 Bookworm
- Debian 13 Trixie

### Ubuntu LTS 系列

- Ubuntu 18.04 Bionic Beaver
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

## 构建方法

选择要构建的版本，使用对应的 Dockerfile：

```
docker build -t debian-bookworm-systemd -f Dockerfile.bookworm .
docker build -t ubuntu-noble-systemd -f Dockerfile.noble .
```

可用的 Dockerfile：

```
Dockerfile.buster
Dockerfile.bullseye
Dockerfile.bookworm
Dockerfile.trixie
Dockerfile.bionic
Dockerfile.focal
Dockerfile.jammy
Dockerfile.noble
```

## 使用方法

### 从 Docker Hub 拉取镜像

```
docker pull sunshinecloud007/systemd-container:debian-bookworm
docker pull sunshinecloud007/systemd-container:ubuntu-noble
```

可用的镜像标签：

```
debian-buster
debian-bullseye
debian-bookworm
debian-trixie
ubuntu-bionic
ubuntu-focal
ubuntu-jammy
ubuntu-noble
```

### 运行容器

```
docker run --detach --privileged \
  --volume=/sys/fs/cgroup:/sys/fs/cgroup:rw \
  --cgroupns=host \
  sunshinecloud007/systemd-container:debian-bookworm
```

### 进入容器

```
docker exec -it [container_id] /bin/bash
```

### 使用 Ansible

```
docker exec --tty [container_id] env TERM=xterm ansible --version
docker exec --tty [container_id] env TERM=xterm ansible-playbook /path/to/playbook.yml
```

### 使用 Ollama

```
docker exec --tty [container_id] ollama run llama2
```

## 自动化构建

本仓库使用 GitHub Actions 自动构建和发布镜像：

- 每周一定时构建所有版本
- 推送到 master 分支时自动构建
- 支持手动触发构建
- 多架构支持：linux/amd64 和 linux/arm64

## 注意事项

本镜像包含预配置的用户和密码，仅用于测试和开发环境。

生产环境使用前请务必：

1. 修改所有用户密码
2. 删除不需要的用户
3. 配置适当的安全策略
4. 使用防火墙限制访问

## 维护者

SunshineCloudTech
