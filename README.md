# 如何创建并发布你自己的 Python 包到 PyPI (使用此模板)

## 简介

这个仓库是一个模板和指南，旨在帮助你快速创建、构建并将你自己的 Python 包发布到 PyPI (Python Package Index)。它提供了一个标准的项目结构和一个详细的分步教程。

这里的 `zephyrzhong` 包只是一个示例，你需要将其替换为你自己的代码。

## 如何使用此模板

1.  **获取模板**: 你可以通过以下任一方式使用此模板：

    - **GitHub 模板**: 点击此页面上方的 "Use this template" 按钮，创建一个基于此模板的新仓库。
    - **克隆**: 克隆此仓库到你的本地机器：
      ```bash
      git clone https://github.com/你的GitHub用户名/你的仓库名.git YourNewProjectName
      cd YourNewProjectName
      ```

2.  **修改核心文件**: 你需要根据你的项目修改以下文件和目录：
    - **`zephyrzhong/` 目录**: 将此目录重命名为你自己的包名 (例如 `my_package/`)。修改或替换里面的 `.py` 文件为你自己的代码。
    - **`setup.py`**: 这是最重要的配置文件。你需要：
      - 修改 `name` 为你在 PyPI 上想要的包名 (必须唯一)。
      - 修改 `version` (初始版本通常是 `0.0.1`)。
      - 更新 `author`, `author_email`, `description`, `url` 等元数据。
      - 确保 `packages=setuptools.find_packages()` 能正确找到你的包目录 (如果你重命名了 `zephyrzhong/`)。
      - 根据需要添加 `install_requires` (你的包的依赖项)。
    - **`README.md` (本文件)**: 将这个文件修改为你自己项目的说明文档。
    - **`LICENSE`**: 选择一个适合你项目的开源许可证，并更新文件内容 (例如，替换版权所有者和年份)。
    - **`test.py` (或 `tests/` 目录)**: 编写你自己的测试代码。

## 项目结构说明

```
. (项目根目录)
├── your_package_name/    # 你的 Python 包代码存放处 (替换 zephyrzhong)
│   ├── __init__.py       # 标记为 Python 包
│   └── core.py           # 你的核心代码 (或任意 .py 文件)
├── .gitignore            # 告诉 Git 忽略哪些文件 (构建产物, 缓存等)
├── LICENSE               # 你的项目许可证
├── README.md             # 项目说明文档 (就是这个文件)
├── requirements-dev.txt  # 构建和发布所需的工具
├── setup.py              # 包的构建和元数据配置文件
└── test.py               # 示例测试文件 (建议使用更完善的测试框架如 pytest)
```

## 发布你的包：分步指南

以下是将你修改后的 Python 包发布到 PyPI 的详细步骤：

**前提条件:**

1.  **Python 和 pip**: 确保你安装了 Python 以及包管理工具 pip。
2.  **PyPI 账户**: 你需要在 [PyPI](https://pypi.org/) 注册一个账户。
3.  **TestPyPI 账户**: 强烈建议也在 [TestPyPI](https://test.pypi.org/) 注册一个账户，用于在正式发布前进行测试。TestPyPI 是一个独立的测试环境。
4.  **API 令牌**: 为了安全上传，建议在 PyPI 和 TestPyPI 网站上为你的账户生成 API 令牌。
    - 登录 PyPI/TestPyPI -> Account settings -> API tokens -> Add API token。
    - 选择范围 (Scope)，例如限制到特定项目。
    - **复制生成的令牌**，它只显示一次！

**发布步骤:**

1.  **安装构建和上传工具**:
    打开终端或命令提示符，进入你的项目根目录，然后运行：

    ```bash
    pip install -r requirements-dev.txt
    ```

    (这会安装 `requirements-dev.txt` 文件中列出的 `setuptools`, `wheel`, `twine`)

2.  **配置 API 令牌 (推荐)**:
    为了避免每次上传都输入密码，可以在你的用户主目录 (`C:\Users\你的用户名\` 或 `~/.pypirc`) 下创建或编辑一个名为 `.pypirc` 的文件，内容类似：

    ```ini
    [testpypi]
      username = __token__
      password = YOUR_TESTPYPI_API_TOKEN # 替换为你的 TestPyPI 令牌

    [pypi]
      username = __token__
      password = YOUR_PYPI_API_TOKEN     # 替换为你的 PyPI 令牌
    ```

    **注意**: 保护好此文件，不要上传到 Git (`.gitignore` 已包含它)。

3.  **确认/更新版本号**:
    在 `setup.py` 文件中设置或更新你的包的 `version` 号。每次发布新版本（包括首次发布或代码更新后），都需要增加版本号。

    ```python
    # setup.py
    setuptools.setup(
        name="YourPackageName", # 确保与 PyPI 上的包名一致
        version="0.1.0",      # 设置或更新版本号!
        # ... 其他配置 ...
    )
    ```

4.  **清理旧的构建文件 (可选但推荐)**:
    删除之前构建时生成的目录，确保是干净的构建。

    ```bash
    # 在 Windows 命令提示符或 PowerShell 中
    rmdir /s /q build dist YourPackageName.egg-info
    # 注意：将 YourPackageName.egg-info 替换为实际生成的 .egg-info 目录名
    ```

    (在 macOS/Linux 上使用 `rm -rf build dist YourPackageName.egg-info`)

5.  **构建分发包**:
    运行以下命令来创建源代码分发 (sdist) 和 wheel 包 (bdist_wheel)。

    ```bash
    python setup.py sdist bdist_wheel
    ```

    这会在 `dist/` 目录下生成 `.tar.gz` 和 `.whl` 文件。

6.  **上传到 TestPyPI (测试)**:
    使用 `twine` 将包上传到 TestPyPI 进行测试。

    ```bash
    twine upload --repository testpypi dist/*
    ```

    如果遇到 503 错误，可能是 TestPyPI 服务器临时问题，请稍后再试。

7.  **从 TestPyPI 安装测试**:
    在另一个环境或卸载当前版本后，尝试从 TestPyPI 安装你的包，验证是否成功。

    ```bash
    pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple/ YourPackageName
    # 运行一些代码检查包是否正常工作
    # python -c "import yourpackagename; ..."
    ```

8.  **上传到 PyPI (正式发布)**:
    如果 TestPyPI 测试成功，就可以上传到正式的 PyPI 了。

    ```bash
    twine upload dist/*
    ```

9.  **验证正式发布**:
    等待几分钟后，你应该可以在 PyPI 网站上看到你的包，并且可以通过 `pip install YourPackageName` 来安装它。

## 本地开发与测试 (可选)

1.  (推荐) 创建并激活虚拟环境:
    ```bash
    python -m venv venv
    # Windows: venv\Scripts\activate
    # macOS/Linux: source venv/bin/activate
    ```
2.  安装开发依赖和包 (可编辑模式):
    ```bash
    pip install -e .
    pip install -r requirements-dev.txt
    # pip install pytest # 如果使用 pytest
    ```
3.  运行测试:
    ```bash
    # python test.py
    # 或者 pytest tests/
    ```

## 模板许可证

本模板项目本身使用 MIT 许可证。详情请见 [LICENSE](LICENSE) 文件。你可以为你自己的项目选择不同的许可证。
