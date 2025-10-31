# 飞牛 fnOS 应用开发手册
声明：基于飞牛官方应用包结合AI分析整理的应用开发手册，仅供学习和开发参考使用
声明：基于飞牛官方应用包结合AI分析整理的应用开发手册，仅供学习和开发参考使用
声明：基于飞牛官方应用包结合AI分析整理的应用开发手册，仅供学习和开发参考使用
## 目录
1. [概述](#概述)
2. [应用包结构](#应用包结构)
3. [manifest 清单文件](#manifest-清单文件)
4. [config 配置目录](#config-配置目录)
5. [cmd 命令脚本目录](#cmd-命令脚本目录)
6. [wizard 向导目录](#wizard-向导目录)
7. [应用生命周期](#应用生命周期)
8. [开发最佳实践](#开发最佳实践)
9. [示例代码](#示例代码)

---

## 概述

飞牛 fnOS 应用包采用 `.tpk` 格式，是一个标准化的应用分发格式。每个应用包包含应用的所有必要文件、配置和元数据。

### 核心特性
- **标准化打包**：统一的目录结构和配置格式
- **生命周期管理**：支持安装、升级、卸载等完整生命周期
- **权限控制**：细粒度的用户权限和资源访问控制
- **向导支持**：可视化安装/卸载向导
- **多架构支持**：支持 x86_64、arm64 等多种架构

---

## 应用包结构

一个完整的 `.tpk` 应用包包含以下文件和目录：

```
your-app-version-tpk/
├── manifest                # 应用清单文件（必需）
├── ICON.PNG               # 应用图标 (必需)
├── ICON_256.PNG           # 256x256 应用图标 (必需)
├── app.tgz                # 应用主体压缩包（必需）
├── config/                # 配置目录（必需）
│   ├── privilege         # 权限配置文件
│   └── resource          # 资源配置文件
├── cmd/                   # 命令脚本目录（必需）
│   ├── config            # 应用配置脚本
│   ├── common            # 通用函数脚本
│   ├── service           # 服务管理脚本
│   ├── package           # 应用包管理脚本
│   ├── main              # 主服务控制脚本
│   ├── install_init      # 安装前钩子
│   ├── install_callback  # 安装后钩子
│   ├── upgrade_init      # 升级前钩子
│   ├── upgrade_callback  # 升级后钩子
│   ├── uninstall_init    # 卸载前钩子
│   └── uninstall_callback # 卸载后钩子
└── wizard/                # 向导目录（可选）
    ├── install           # 安装向导配置
    └── uninstall         # 卸载向导配置
```

---

## manifest 清单文件

`manifest` 文件定义应用的元数据和配置信息，采用键值对格式。

### 必需字段

| 字段名 | 类型 | 说明 | 示例 |
|--------|------|------|------|
| `appname` | String | 应用唯一标识符，建议使用反向域名格式 | `trim.photos` |
| `version` | String | 应用版本号，建议使用语义化版本 | `0.8.57` |
| `display_name` | String | 应用显示名称 | `相册` |
| `desc` | String | 应用描述，支持 HTML 标签 | `飞牛官方出品...` |
| `arch` | String | 目标架构 | `x86_64` |
| `maintainer` | String | 维护者名称 | `飞牛` |
| `maintainer_url` | String | 维护者网址 | `https://www.fnnas.com/` |
| `distributor` | String | 分发者名称 | `飞牛` |
| `distributor_url` | String | 分发者网址 | `https://www.fnnas.com/` |

### 可选字段

| 字段名 | 类型 | 说明 | 默认值 |
|--------|------|------|--------|
| `os_min_version` | String | 最低系统版本要求 | - |
| `helpurl` | String | 帮助文档链接 | - |
| `changelog` | String | 版本更新日志，支持 HTML | - |
| `source` | String | 应用来源 | `official` / `community` |
| `install_type` | String | 安装类型 | `root` / `user` |
| `checkport` | String | 是否检查端口 | `true` / `false` |
| `service_port` | String | 服务端口号 | - |
| `desktop_uidir` | String | 桌面 UI 目录 | `ui` |
| `desktop_applaunchname` | String | 应用启动名称 | - |
| `micro_app` | String | 是否为微应用 | `true` / `false` |
| `iframe_app` | String | 是否为 iframe 应用 | `true` / `false` |
| `native_app` | String | 是否为原生应用 | `true` / `false` |
| `disable_authorization_path` | String | 禁用授权路径 | `true` / `false` |
| `checksum` | String | 校验和 | - |

### 示例

```ini
version="0.8.57"
appname="trim.photos"
desc="飞牛官方出品，相册存储美好时光。<br>1、App照片备份，原图备份..."
display_name="相册"
maintainer="飞牛"
arch="x86_64"
maintainer_url="https://www.fnnas.com/"
distributor="飞牛"
distributor_url="https://www.fnnas.com/"
os_min_version="0.9.21"
desktop_uidir="ui"
desktop_applaunchname="trim.photos"
source="official"
install_type="root"
checkport="false"
disable_authorization_path="true"
native_app="true"
```

---

## config 配置目录

### privilege 权限配置

定义应用的运行权限和用户/组配置，采用 JSON 格式。

```json
{
  "defaults": {
    "run-as": "root"
  },
  "username": "trim.photos",
  "groupname": "trim.photos"
}
```

**字段说明：**
- `defaults.run-as`：应用运行身份（`root` 或 `user`）
- `username`：应用运行用户名
- `groupname`：应用运行组名

### resource 资源配置

定义应用所需的系统资源，采用 JSON 格式。

#### systemd-unit 配置

最简单的配置（仅声明使用 systemd）：

```json
{
  "systemd-unit": {}
}
```

#### data-share 配置

定义共享数据目录和权限：

```json
{
  "data-share": {
    "shares": [
      {
        "name": "trim.iscsi",
        "permission": {
          "rw": ["trim.iscsi"]
        }
      },
      {
        "name": "trim.iscsi/data",
        "permission": {
          "rw": ["trim.iscsi"]
        }
      }
    ]
  }
}
```

**字段说明：**
- `shares`：共享目录数组
- `name`：共享目录名称（相对路径）
- `permission.rw`：读写权限用户列表
- `permission.ro`：只读权限用户列表（可选）

---

## cmd 命令脚本目录

所有脚本均为 Bash 脚本，必须包含 `#!/bin/bash` shebang。

### 1. config 配置脚本

定义应用的基础配置变量。

```bash
#!/bin/bash

# -- 基础配置 --
# 防火墙配置文件
FWPORTS_FILE="${TRIM_PKGETC}/${TRIM_APPNAME}.sc"
# 端口号（-1 表示不使用）
PORT=-1

# -- 运行配置 --

# 环境变量
export INFRA_DB_DSN="${TRIM_PKGVAR}/db/main.db"
export INFRA_LOGGER_PATH="${TRIM_PKGVAR}/log"

# 执行文件位置
BIN="${TRIM_APPDEST}/your-app-binary"
# 日志文件
LOG_FILE="${TRIM_PKGVAR}/${TRIM_APPNAME}.log"
# PID 文件
PID_FILE="${TRIM_PKGVAR}/${TRIM_APPNAME}.pid"
# 运行目录
SVC_CWD=${TRIM_PKGVAR}
# 运行超时（秒）
SVC_WAIT_TIMEOUT=15
# 是否后台执行
SVC_BACKGROUND=y
# 是否写入PID文件
SVC_WRITE_PID=y
# 是否不输出日志
SVC_QUIET=
```

### 2. common 通用函数脚本

提供通用工具函数。

```bash
#!/bin/bash

# 打印日志
# 用法: print_log "text..." 或 print_log "module" "text..."
print_log() {
    if [ -z "${SVC_QUIET}" ]; then
        local _msg_="$@"
        if [ -z "${_msg_}" ]; then
            while IFS=$'\n' read -r line; do
                print_log "${line}"
            done
        else
            echo -e "$(date +'%Y/%m/%d %H:%M:%S')\t${_msg_}" 1>&2
        fi
    fi
}

# 打印步骤日志
step_log() {
    print_log "===> Step $1. TRIM_APP_STATUS=${TRIM_APP_STATUS}"
}

# 检查并创建必要目录
check_dir() {
    echo "call func: check_dir"
    
    # 检查日志目录
    if [ ! -d "${TRIM_PKGVAR}/log" ]; then
        echo "  create log dir"
        mkdir -p "${TRIM_PKGVAR}/log"
    fi
    
    # 检查数据目录
    if [ ! -d "${TRIM_PKGVAR}/data" ]; then
        echo "  create data dir"
        mkdir -p "${TRIM_PKGVAR}/data"
    fi
}

# 使用BIN路径查找进程PID
find_process_using_bin() {
    pid=$(pgrep -f "^${BIN}")
    if [ -n "$pid" ]; then
        return $pid
    fi
    return 0
}
```

### 3. service 服务管理脚本

实现服务的启动、停止、状态检查功能。

```bash
#!/bin/bash

# 启动服务
start_daemon() {
    print_log "start_daemon" "Service is not running, starting..."
    
    # 切换工作目录
    if [ -n "${SVC_CWD}" ]; then
        cd ${SVC_CWD}
    fi
    
    # 后台或前台运行
    if [ -z "${SVC_BACKGROUND}" ]; then
        env ${BIN} 2>&1
    else
        env ${BIN} 2>&1 &
    fi
    
    pid=$!
    
    # 写入PID文件
    if [ -n "${SVC_WRITE_PID}" ] && [ -n "${SVC_BACKGROUND}" ]; then
        printf "%s" "$pid" >${PID_FILE}
    fi
    
    # 等待服务启动
    sleep 5
    
    print_log "start_daemon" "Service started, PID: $pid"
}

# 停止服务
stop_daemon() {
    local max_retries=30
    local retry_interval=1
    
    # 检查PID文件有效性
    if check_pid_file_valid; then
        print_log "stop_daemon" "Stopping service with PID ${pid}..."
        kill "${pid}"
        
        if ! wait_for_process_stop "${max_retries}" "${retry_interval}"; then
            print_log "stop_daemon" "Failed to stop process ${pid}"
            return 1
        fi
        
        cleanup_pid_file
        return 0
    fi
    
    # 通过二进制路径查找进程
    if find_process_by_binary; then
        print_log "stop_daemon" "Stopping process ${pid}..."
        kill "${pid}"
        
        if ! wait_for_process_stop "${max_retries}" "${retry_interval}"; then
            return 1
        fi
        return 0
    fi
    
    print_log "stop_daemon" "No valid process found"
    return 0
}

# 检查PID文件有效性
check_pid_file_valid() {
    if [[ -r "${PID_FILE}" ]]; then
        pid=$(cat "${PID_FILE}")
        if ps -p "${pid}" >/dev/null 2>&1 && \
           [[ "$(readlink -f /proc/${pid}/exe)" == "${BIN}" ]]; then
            return 0
        fi
        cleanup_pid_file
    fi
    return 1
}

# 通过二进制路径查找进程
find_process_by_binary() {
    local target_pid=$(pgrep -f "^${BIN}")
    
    if [ -n "${target_pid}" ]; then
        local exe_path=$(readlink -f /proc/${target_pid}/exe)
        
        if [ "${exe_path}" = "${BIN}" ]; then
            pid=${target_pid}
            printf "%s" "${pid}" >"${PID_FILE}"
            print_log "find_process_by_binary" "Found process ${pid}"
            return 0
        fi
    fi
    return 1
}

# 等待进程停止
wait_for_process_stop() {
    local max_retries=$1
    local interval=$2
    local retry_count=0
    
    while (( retry_count < max_retries )); do
        if status_daemon >/dev/null 2>&1; then
            print_log "stop_daemon" "Process still running (${retry_count}/${max_retries})"
            sleep "${interval}"
            ((retry_count++))
        else
            return 0
        fi
    done
    return 1
}

# 清理PID文件
cleanup_pid_file() {
    if [[ -f "${PID_FILE}" ]]; then
        rm -f "${PID_FILE}"
        print_log "stop_daemon" "Cleaned up PID file"
    fi
}

# 检查服务状态
status_daemon() {
    # 检查PID文件
    if [ -r "${PID_FILE}" ]; then
        pid=$(cat "${PID_FILE}")
        
        if ps -p "${pid}" >/dev/null 2>&1; then
            exe_path=$(readlink -f /proc/${pid}/exe)
            
            if [ "${exe_path}" = "${BIN}" ]; then
                print_log "status_daemon" "Service is running, PID: $pid"
                return 0
            fi
        fi
    fi
    
    # 尝试通过二进制路径查找
    pid=$(pgrep -f "^${BIN}")
    if [ -n "${pid}" ]; then
        exe_path=$(readlink -f /proc/${pid}/exe)
        
        if [ "${exe_path}" = "${BIN}" ]; then
            printf "%s" "$pid" >${PID_FILE}
            print_log "status_daemon" "Found process, PID: ${pid}"
            return 0
        fi
    fi
    
    return 1
}
```

### 4. main 主控制脚本

服务的主要入口脚本，处理 start、stop、status 命令。

```bash
#!/bin/bash

. $(dirname $0)"/config"
. $(dirname $0)"/common"
. $(dirname $0)"/service"

case $1 in
start)
    if status_daemon; then
        print_log "already running" >>${LOG_FILE}
        exit 0
    else
        print_log "Starting ..." >>${LOG_FILE}
        start_daemon
        exit $?
    fi
    ;;
stop)
    stop_daemon
    exit $?
    ;;
status)
    status_daemon
    exit $?
    ;;
*)
    exit 1
    ;;
esac
```

### 5. package 应用包管理脚本

定义应用生命周期各阶段的处理函数。

```bash
#!/bin/bash

# 安装初始化
install_init() {
    step_log "install_init"
    # 安装前的准备工作
    exit 0
}

# 安装回调
install_callback() {
    step_log "install_callback"
    # 创建必要目录
    check_dir
    # 其他安装后处理
    exit 0
}

# 卸载初始化
uninstall_init() {
    step_log "uninstall_init"
    # 卸载前的准备工作
    exit 0
}

# 卸载回调
uninstall_callback() {
    step_log "uninstall_callback"
    # 清理数据、配置等
    if [ "$wizard_delete_data" = "true" ]; then
        print_log "Deleting application data..."
        rm -rf "${TRIM_PKGVAR}/data"
    fi
    exit 0
}

# 升级初始化
upgrade_init() {
    step_log "upgrade_init"
    # 升级前的准备工作
    exit 0
}

# 升级回调
upgrade_callback() {
    step_log "upgrade_callback"
    # 升级后的处理工作
    exit 0
}
```

### 6. install_init / install_callback

安装前后的钩子脚本。

```bash
#!/bin/bash

. $(dirname $0)"/config"
. $(dirname $0)"/common"
. $(dirname $0)"/package"
$(basename $0) > $TRIM_TEMP_LOGFILE
```

### 7. upgrade_init / upgrade_callback

升级前后的钩子脚本，结构同上。

### 8. uninstall_init / uninstall_callback

卸载前后的钩子脚本，结构同上。

---

## wizard 向导目录

向导采用 JSON 格式定义，用于创建用户交互界面。

### 卸载向导示例

**文件路径：** `wizard/uninstall`

```json
[{
  "stepTitle": "卸载应用",
  "items": [{
    "type": "radio",
    "field": "wizard_delete_data",
    "label": "保留或删除应用数据",
    "options": [{
      "label": "仅卸载，保留数据在原存储空间",
      "value": "false"
    }, {
      "label": "删除应用数据(不可恢复)",
      "value": "true"
    }],
    "rules": [
      {
        "required": true,
        "message": "请选择保留或删除应用数据"
      }
    ]
  }]
}]
```

### 安装向导示例

```json
[{
  "stepTitle": "配置应用",
  "items": [
    {
      "type": "text",
      "field": "app_port",
      "label": "服务端口",
      "defaultValue": "8080",
      "rules": [
        {
          "required": true,
          "message": "请输入服务端口"
        },
        {
          "pattern": "^[0-9]+$",
          "message": "端口必须是数字"
        }
      ]
    },
    {
      "type": "password",
      "field": "admin_password",
      "label": "管理员密码",
      "rules": [
        {
          "required": true,
          "message": "请输入管理员密码"
        },
        {
          "min": 8,
          "message": "密码长度至少8位"
        }
      ]
    }
  ]
}]
```

### 支持的表单组件类型

| 类型 | 说明 | 示例 |
|------|------|------|
| `text` | 文本输入框 | 用户名、端口号 |
| `password` | 密码输入框 | 密码 |
| `number` | 数字输入框 | 端口号、数量 |
| `radio` | 单选按钮组 | 选择选项 |
| `checkbox` | 复选框 | 多选选项 |
| `select` | 下拉选择框 | 下拉列表 |
| `textarea` | 多行文本框 | 长文本 |

### 验证规则

| 规则 | 说明 | 示例 |
|------|------|------|
| `required` | 必填项 | `{"required": true, "message": "必填"}` |
| `pattern` | 正则表达式 | `{"pattern": "^[0-9]+$", "message": "仅数字"}` |
| `min` | 最小长度/值 | `{"min": 8, "message": "至少8位"}` |
| `max` | 最大长度/值 | `{"max": 100, "message": "不超过100"}` |

---

## 应用生命周期

### 1. 安装流程

```
开始安装
    ↓
install_init (安装前钩子)
    ↓
解压 app.tgz
    ↓
创建用户/组 (根据 privilege 配置)
    ↓
配置资源 (根据 resource 配置)
    ↓
install_callback (安装后钩子)
    ↓
启动服务 (调用 cmd/main start)
    ↓
安装完成
```

### 2. 升级流程

```
开始升级
    ↓
upgrade_init (升级前钩子)
    ↓
停止服务 (调用 cmd/main stop)
    ↓
备份旧版本
    ↓
解压新版本 app.tgz
    ↓
upgrade_callback (升级后钩子)
    ↓
启动服务 (调用 cmd/main start)
    ↓
升级完成
```

### 3. 卸载流程

```
开始卸载
    ↓
显示卸载向导 (如果存在)
    ↓
uninstall_init (卸载前钩子)
    ↓
停止服务 (调用 cmd/main stop)
    ↓
uninstall_callback (卸载后钩子)
    ↓
删除应用文件
    ↓
删除用户/组 (可选)
    ↓
删除数据 (根据用户选择)
    ↓
卸载完成
```

### 4. 启动/停止/状态检查

```bash
# 启动服务
/var/apps/your-app/cmd/main start

# 停止服务
/var/apps/your-app/cmd/main stop

# 检查状态
/var/apps/your-app/cmd/main status
```

---

## 开发最佳实践

### 1. 目录结构规范

- 所有应用文件应打包到 `app.tgz` 中
- 运行时数据存储在 `${TRIM_PKGVAR}` 目录
- 应用二进制文件解压到 `${TRIM_APPDEST}` 目录
- 日志文件统一存储在 `${TRIM_PKGVAR}/log`

### 2. 环境变量

系统提供的环境变量：

| 变量名 | 说明 | 示例 |
|--------|------|------|
| `TRIM_APPNAME` | 应用名称 | `trim.photos` |
| `TRIM_APPDEST` | 应用安装目录 | `/var/apps/trim.photos/target` |
| `TRIM_PKGVAR` | 应用数据目录 | `/var/apps/trim.photos/var` |
| `TRIM_PKGETC` | 应用配置目录 | `/var/apps/trim.photos/etc` |
| `TRIM_DATA_SHARE_PATHS` | 共享数据路径 | - |
| `TRIM_TEMP_LOGFILE` | 临时日志文件 | - |
| `TRIM_APP_STATUS` | 应用状态 | `install` / `upgrade` / `uninstall` |
| `TRIM_RUN_USERNAME` | 运行用户名 | - |
| `TRIM_RUN_GROUPNAME` | 运行组名 | - |

### 3. 日志管理

- 统一使用 `print_log` 函数记录日志
- 日志格式：`YYYY/MM/DD HH:MM:SS  [模块] 消息`
- 日志文件存储在 `${TRIM_PKGVAR}/${TRIM_APPNAME}.log`

### 4. 进程管理

- 使用 PID 文件管理进程
- 停止服务时先发送 TERM 信号，超时后使用 KILL
- 启动前检查服务状态，避免重复启动

### 5. 错误处理

- 所有脚本必须返回正确的退出码（0=成功，非0=失败）
- 关键操作前检查前置条件
- 使用 `set -e` 在脚本开头可自动处理错误

### 6. 向导设计

- 仅在必要时使用向导
- 提供合理的默认值
- 验证用户输入
- 清晰的错误提示

### 7. 资源清理

- 卸载时清理临时文件和日志
- 根据用户选择决定是否删除数据
- 清理符号链接和空目录

### 8. 安全性

- 避免硬编码密码和密钥
- 使用环境变量传递敏感信息
- 正确设置文件权限
- 输入验证和过滤

### 9. systemd 服务集成

对于需要 systemd 管理的应用：

```bash
# 在 app.tgz 中包含 systemd unit 文件
your-app.service

# 在 install_callback 中启用服务
systemctl enable your-app.service
systemctl start your-app.service

# 在 uninstall_callback 中停用服务
systemctl stop your-app.service
systemctl disable your-app.service
```

### 10. 多用户支持

如果应用支持多用户：

```bash
# 在 privilege 中定义用户组
{
  "username": "your-app",
  "groupname": "your-app-users"
}

# 在代码中处理用户隔离
USER_DATA_DIR="${TRIM_PKGVAR}/users/${USER_ID}"
```

---

## 示例代码

### 完整的应用开发示例

#### 1. 创建项目结构

```bash
mkdir -p my-app-1.0.0-tpk/{config,cmd,wizard}
cd my-app-1.0.0-tpk
```

#### 2. 编写 manifest

```bash
cat > manifest << 'EOF'
version="1.0.0"
appname="com.example.myapp"
desc="这是一个示例应用"
display_name="我的应用"
maintainer="Your Name"
arch="x86_64"
maintainer_url="https://example.com"
distributor="Your Company"
distributor_url="https://example.com"
os_min_version="0.9.0"
desktop_uidir="ui"
desktop_applaunchname="com.example.myapp"
source="community"
install_type="root"
checkport="false"
service_port="8080"
EOF
```

#### 3. 配置权限

```bash
cat > config/privilege << 'EOF'
{
  "defaults": {
    "run-as": "root"
  },
  "username": "myapp",
  "groupname": "myapp"
}
EOF
```

#### 4. 配置资源

```bash
cat > config/resource << 'EOF'
{
  "systemd-unit": {}
}
EOF
```

#### 5. 编写脚本

**config 脚本：**

```bash
cat > cmd/config << 'EOF'
#!/bin/bash

BIN="${TRIM_APPDEST}/myapp"
LOG_FILE="${TRIM_PKGVAR}/${TRIM_APPNAME}.log"
PID_FILE="${TRIM_PKGVAR}/${TRIM_APPNAME}.pid"
SVC_CWD=${TRIM_PKGVAR}
SVC_WAIT_TIMEOUT=15
SVC_BACKGROUND=y
SVC_WRITE_PID=y
EOF
chmod +x cmd/config
```

**common 脚本：**

```bash
cat > cmd/common << 'EOF'
#!/bin/bash

print_log() {
    echo -e "$(date +'%Y/%m/%d %H:%M:%S')\t$@" 1>&2
}

step_log() {
    print_log "===> Step $1"
}

check_dir() {
    [ ! -d "${TRIM_PKGVAR}/log" ] && mkdir -p "${TRIM_PKGVAR}/log"
    [ ! -d "${TRIM_PKGVAR}/data" ] && mkdir -p "${TRIM_PKGVAR}/data"
}
EOF
chmod +x cmd/common
```

**service 脚本：** (使用前面提供的完整版本)

**main 脚本：** (使用前面提供的完整版本)

**package 脚本：** (使用前面提供的完整版本)

**钩子脚本：**

```bash
# 创建所有钩子脚本
for hook in install_init install_callback upgrade_init upgrade_callback uninstall_init uninstall_callback; do
    cat > cmd/$hook << 'EOF'
#!/bin/bash

. $(dirname $0)"/config"
. $(dirname $0)"/common"
. $(dirname $0)"/package"
$(basename $0) > $TRIM_TEMP_LOGFILE
EOF
    chmod +x cmd/$hook
done
```

#### 6. 创建卸载向导

```bash
cat > wizard/uninstall << 'EOF'
[{
  "stepTitle": "卸载应用",
  "items": [{
    "type": "radio",
    "field": "wizard_delete_data",
    "label": "保留或删除应用数据",
    "options": [{
      "label": "仅卸载，保留数据",
      "value": "false"
    }, {
      "label": "删除应用数据(不可恢复)",
      "value": "true"
    }],
    "rules": [{
      "required": true,
      "message": "请选择选项"
    }]
  }]
}]
EOF
```

#### 7. 准备应用文件

```bash
# 创建应用目录结构
mkdir -p app-files/ui
mkdir -p app-files/bin

# 复制你的应用二进制文件
cp /path/to/your/myapp app-files/bin/

# 复制 UI 文件
cp -r /path/to/your/ui/* app-files/ui/

# 打包应用
cd app-files
tar czf ../app.tgz *
cd ..
rm -rf app-files
```

#### 8. 准备图标

```bash
# 准备两个PNG图标文件
cp /path/to/icon.png ICON.PNG
cp /path/to/icon_256.png ICON_256.PNG
```

#### 9. 打包应用

```bash
cd ..
tar czf my-app-1.0.0.tpk -C my-app-1.0.0-tpk .
```

#### 10. 测试应用

```bash
# 在飞牛系统上安装应用
# 通过 Web 界面或命令行工具安装

# 检查应用状态
/var/apps/com.example.myapp/cmd/main status

# 查看日志
tail -f /var/apps/com.example.myapp/var/com.example.myapp.log
```

---

## 调试技巧

### 1. 查看安装日志

```bash
# 系统安装日志
tail -f /var/log/trim/appmanager.log

# 应用日志
tail -f /var/apps/your-app/var/your-app.log
```

### 2. 手动测试脚本

```bash
# 进入应用目录
cd /var/apps/your-app

# 设置环境变量
export TRIM_APPNAME="your-app"
export TRIM_APPDEST="/var/apps/your-app/target"
export TRIM_PKGVAR="/var/apps/your-app/var"
export TRIM_PKGETC="/var/apps/your-app/etc"

# 测试启动
./cmd/main start

# 测试状态
./cmd/main status

# 测试停止
./cmd/main stop
```

### 3. 检查进程

```bash
# 查看进程
ps aux | grep your-app

# 查看PID文件
cat /var/apps/your-app/var/your-app.pid

# 检查进程详情
ls -l /proc/$(cat /var/apps/your-app/var/your-app.pid)/exe
```

### 4. 权限问题

```bash
# 检查文件权限
ls -la /var/apps/your-app

# 检查用户和组
id your-app-user

# 修复权限
chown -R your-app-user:your-app-group /var/apps/your-app/var
```

---

## 常见问题

### Q1: 应用无法启动？

**排查步骤：**
1. 检查二进制文件权限和路径
2. 查看日志文件找出错误信息
3. 验证环境变量是否正确
4. 检查端口是否被占用
5. 确认依赖服务是否运行

### Q2: 卸载后数据未删除？

**解决方案：**
在 `uninstall_callback` 中正确处理数据删除逻辑，检查向导字段值。

### Q3: 升级后配置丢失？

**解决方案：**
在 `upgrade_init` 中备份配置，在 `upgrade_callback` 中恢复。

### Q4: 服务频繁重启？

**排查步骤：**
1. 检查日志找出崩溃原因
2. 验证资源限制（内存、CPU）
3. 检查文件描述符限制
4. 查看系统日志 `journalctl -xe`

### Q5: 如何支持多架构？

**解决方案：**
- 为每个架构创建单独的 .tpk 包
- 在 manifest 中指定 `arch` 字段
- 在 app.tgz 中包含对应架构的二进制文件

---

## 参考资源

- 飞牛官方文档: https://www.fnnas.com/
- 示例应用代码: 参考本手册附带的示例包
- 社区论坛: https://forum.fnnas.com/

---

## 版本历史

- v1.0 (2025-10-31): 初始版本，基于官方应用包分析

---

## 许可证

本手册基于飞牛官方应用包分析整理，仅供学习和开发参考使用。

## 贡献

欢迎提交问题和改进建议！

