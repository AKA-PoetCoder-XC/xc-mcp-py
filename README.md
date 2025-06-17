# MCP开发指南（python版）

提示：以下所有的xxx-\*或xxx_\*都可以换成自己自定义的名称

## 1 开发环境配置

1 使用uv创建一个方便打包发布的mcp工程

```
uv init --package --python 3.10 xxx-mcp
```

2 进入项目目录

```
cd xxx-mcp
```

3 创建venv虚拟环境

```
uv venv
```

4 激活虚拟环境

```
# 在MacOs/Linux当中激活虚拟环境
source .venv/bin/activate
# 在Windows当中激活虚拟环境
.venv\Scripts\activate
```

5 安装mcp开发相关外部库，这里只安装了基础的mcp[cli]和httpx库

```
uv add "mcp[cli]" httpx
```

## 2 mcp服务开发

mcp 服务有两种类型
1 stdio，在主机本地运行的mcp服务，通常通过配置uvx或npx命令拉取远程中央仓库的包在本地调用服务

2 sse，在远程主机上运行的mcp服务，通常用于跨主机调用服务

### 2.1 stdio类型mcp服务开发

1 新建xxx_stdio_server.py，用于编写具体业务逻辑

2 在xxx_stdio_server.py中编写代码

```python
from typing import Any
import httpx
from mcp.server.fastmcp import FastMCP

# 初始化 FastMCP server
mcp = FastMCP("xxx_stdio_server")

# 示例工具
@mcp.tool()
async def xxx(
    param: str
) -> str:
    """工具说明

    Args:
    
    param (str): 参数说明

    Returns:
        str: 运行结果说明

    """
    return '调用成功!'

# 暴露给__init__.py用
def run():
    mcp.run(transport='stdio')
    
if __name__ == "__main__":
    # 初始化并运行 server
   run()
```

3 在\_\_init\_\_.py中引入启动该mcp服务的函数进行调用，方便后续在mcp客户端配置uvx拉取项目的时候启动服务

```python
from .xxx_stdio_server import run

def main() -> None:
    run()
```

4 将代码打包

```
uv build
```

5 发布到远程pypi仓库，token需要自己在pypi官网(https://pypi.org/)注册账号生成，这个网上教程很多

```
uv publish --token pypi-xxxxxxxxxx
```

## 3 在任意客户端配置mcp服务

xxx-server可以自定义为自己的服务名称

```json
# windows版
{
  "mcpServers": {
    "xxx-server": {
      "command": "uvx.exe",
      "args": [
        "xxx-mcp"
    }
  }
}

# 通用版
{
  "mcpServers": {
    "xxx-server": {
      "command": "uvx",
      "args": [
        "xxx-mcp"
      ]
    }
  }
}
```
