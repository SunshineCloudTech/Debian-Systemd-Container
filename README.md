# Debian Systemd 容器镜像

基于 Debian Bookworm 的 Docker 容器镜像，支持 systemd、Ansible 和 Ollama。

## 镜像说明

本镜像基于 Debian Bookworm 构建，包含以下主要组件：

- systemd：完整的 systemd 支持
- Ansible：通过 pip 安装的最新版本
- Ollama：用于运行大语言模型
- Python3 和 pip
- sudo、curl、wget 等常用工具

## 预配置用户（记得下载镜像后及时修改密码！！！）

镜像中预配置了以下用户：

- root：密码为 123456789
- Administrator：密码为 123456789，具有 sudo 权限
- matrix0523：密码为 123456789，具有 sudo 权限和 NOPASSWD 配置
- ollama：系统用户，用于运行 Ollama 服务

## 构建方法

1. 安装 Docker
2. 进入本仓库目录
3. 运行以下命令构建镜像：

```
docker build -t debian-systemd .
```

## 使用方法

1. 安装 Docker
2. 构建或拉取镜像
3. 运行容器：

```
docker run --detach --privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:rw --cgroupns=host debian-systemd:latest
```

4. 进入容器：

```
docker exec -it [container_id] /bin/bash
```

5. 在容器内使用 Ansible：

```
docker exec --tty [container_id] env TERM=xterm ansible --version
docker exec --tty [container_id] env TERM=xterm ansible-playbook /path/to/playbook.yml
```

6. 在容器内使用 Ollama：

```
docker exec --tty [container_id] ollama run llama2
```

## 注意事项

本镜像包含预配置的用户和密码，仅用于测试和开发环境。请勿在生产环境中直接使用。如需在生产环境使用，请修改 Dockerfile 中的密码配置，并采取适当的安全措施。

## 维护者

SunshineCloudTech
