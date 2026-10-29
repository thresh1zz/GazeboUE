# GazeboUE

ROS 2 Jazzy + Gazebo Harmonic 单容器仿真环境，包含 50 个机器人、ROS 2 位姿桥接、TCP 位姿输出及域随机化支持。

## 下载

从 [Releases](https://github.com/thresh1zz/GazeboUE/releases) 下载：

- `GazeboUE-single-container-v1.tar.gz`
- `GazeboUE-single-container-v1.tar.gz.sha256`

## 环境要求

- x86-64 Ubuntu 24.04
- Docker Engine 和 Docker Compose 插件
- 建议至少 4 核 CPU、16 GiB 内存
- 首次构建需要访问 Ubuntu、ROS 和 Docker 镜像源

宿主机不需要单独安装 ROS 2 或 Gazebo。

## 启动

```bash
sha256sum -c GazeboUE-single-container-v1.tar.gz.sha256
tar -xzf GazeboUE-single-container-v1.tar.gz
cd GazeboUE-single-container-v1
docker compose -f compose.single.yaml up -d --build
```

检查状态：

```bash
docker compose -f compose.single.yaml ps
docker logs --tail 100 gazeboue-simulator
```

容器状态应显示为 `healthy`。

## 验证

```bash
python3 linux/verify_multi_instance.py --instances 1 --base-port 18765

docker exec gazeboue-simulator bash -lc \
  'source /opt/ros/jazzy/setup.bash; python3 /opt/gazebo_ue/linux/verify_model_states.py'
```

两项结果都应显示 `passed: true`，并分别收到 50 个位姿和 50 个 model states。

## 域随机化

编辑 `domain-randomization.env`，将 `GAZEBO_UE_DOMAIN_RANDOMIZATION` 改为 `1`，然后执行：

```bash
docker compose -f compose.single.yaml up -d --force-recreate
```

本次 episode 的物理参数保存在 `runtime/container_00/domain_randomization.json`。
