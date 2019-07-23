# 如何发布公开 pip 包
## 需求
1. `pypi.org`账号，主邮箱需要验证通过。
2. `testing.pypi.org`账号，**注意：测试环境的`pypi 账号` 和正式环境的 `pypi 账号`不互通**，为了社区的稳定及繁荣发展，请务必先上测试环境确认无误后再发到正式环境。
3. 有一定的 python 基础，了解 `Python2`和共存时该如何使用 `pip` 安装第三方包。

## 开始
> 尽量把自己的包单独放到一个项目内，把自己写好的包在 [github](https://www.github.com) 上新建一个项目。
1. 在 `github`上建一个项目，项目名称尽量和你的包名称一致。
2. 把自己的包放到 `本地环境`，创建一个虚拟环境，方便你`freeze` 你的依赖包，并选择一个[开源协议](http://choosealicense.online/), 上传到 `git`。
3. 编写 `REANDME.md`, 让其他人知道你的工具包如何使用。
4. 编写 `setup.py`, 即编译你的工具包。

    setup.py是setuptools的构建脚本。它告诉setuptools你的包（例如名称和版本）以及要包含的代码文件。

    打开setup.py并输入以下内容。更新软件包名称以包含您的用户名（例如，example-pkg-theacodes），这可确保您拥有唯一的软件包名称，并且您的软件包与本教程后其他人上传的软件包不会发生冲突。
    ```python
    import setuptools

    with open("README.md", "r") as fh:
        long_description = fh.read()
    
    setuptools.setup(
        name="example-pkg-your-username",                  # 包名称
        version="0.0.1",                                   # 包版本
        author="Example Author",                           # 作者
        license='MIT',                                     # 协议简写
        author_email="author@example.com",                 # 作者邮箱
        description="A small example package",             # 工具包简单描述
        long_description=long_description,                 # readme 部分
        long_description_content_type="text/markdown",     # readme 文件类型
        install_requires=[                                 # 工具包的依赖包
        'certifi>=2019.6.16',
        'chardet>=3.0.4',
        'decorator>=4.4.0',
        'idna>=2.8',
        'requests>=2.22.0',
        'retry>=0.9.2',
        'urllib3>=1.25.3',
        'xmltodict>=0.12.0'
        ],
        url="https://github.com/pypa/sampleproject",       # 包的开源链接
        packages=setuptools.find_packages(),               # 不用动，会自动发现
        classifiers=[                                      # 给出了指数和点子你的包一些额外的元数据
            "Programming Language :: Python :: 3",
            "License :: OSI Approved :: MIT License",
            "Operating System :: OS Independent",
        ],
    )
    ```
    - name是包的分发名称。只要包含字母，数字_和，就可以是任何名称-。它也不能在pypi.org上使用。请务必使用您的用户名更新此内容，因为这样可确保您不会尝试上传与上传程序包时已存在的程序包相同的程序包。
    - version 是包版本看 PEP 440有关版本的更多详细信息。
    - author并author_email用于识别包的作者。
    - description 是一个简短的，一句话的包的总结。
    - long_description是包的详细说明。这显示在Python Package Index的包详细信息包中。在这种情况下，加载长描述README.md是一种常见模式。
    - long_description_content_type告诉索引什么类型的标记用于长描述。在这种情况下，它是Markdown。
    - url是项目主页的URL。对于许多项目，这只是一个指向GitHub，GitLab，Bitbucket或类似代码托管服务的链接。
    - packages是应包含在分发包中的所有Python 导入包的列表。我们可以使用 自动发现所有包和子包，而不是手动列出每个包。在这种情况下，包列表将是example_pkg，因为它是唯一存在的包。find_packages()
    - classifiers给出了指数和点子你的包一些额外的元数据。在这种情况下，该软件包仅与Python 3兼容，根据MIT许可证进行许可，并且与操作系统无关。您应始终至少包含您的软件包所使用的Python版本，软件包可用的许可证以及您的软件包将使用的操作系统。有关分类器的完整列表，请参阅 https://pypi.org/classifiers/。

5. 生成分发档案
    > 是为包生成分发包。这些是上传到包索引的档案，可以通过pip安装。
    - 确认 `setuptools` 和 `whell` 为最新版 **【重要】: 如果不为最新版可能导致无法编译。**
    ```shell
    pip install --user --upgrade setuptools wheel   
    # 如果您在安装它们时遇到问题，请参阅 [安装包教程](https://github.com/pypa/packaging-problems/issues/new?title=Trouble+following+packaging+libraries+tutorial) 
    ```
    - 从setup.py位于的同一目录运行此命令：
    ```shell
    python3 setup.py sdist bdist_wheel
    ```

6. 上传编译包到测试环境
   > Test PyPI是用于测试和实验的包索引的单独实例。这对于像我们不一定想要上传到真实索引的本教程这样的东西很棒。要注册帐户，请访问 https://test.pypi.org/account/register/并完成该页面上的步骤。在您上传任何套餐之前，您还需要验证您的电子邮件地址。有关Test PyPI的更多详细信息，请参阅 使用[TestPyPI](https://packaging.python.org/guides/using-testpypi/)。
   
   安装 `twine` 上传编译包
   ```shell
   pip install --user --upgrade twine
   ```
   使用 `twine` 上传编译包
   ```shell
   python -m twine upload --repository-url https://test.pypi.org/legacy/ dist/*
    ```
    到现在已经上传完成了，你可以到你的包，并安装测试。 [test.pypi.org](https://test.pypi.org)


7. 上传编译包到正式环境
    > 确认包完好，可以正常工作，上传到正式环境。
    
    使用 `twine` 上传编译包
    ```shell
    python -m twine upload dist/*
    ```
 8. 测试正式环境的包
 
 ## 参考链接
 [打包Python项目](https://packaging.python.org/tutorials/packaging-projects/)
 
 [使用TestPyPI](https://packaging.python.org/guides/using-testpypi/)
 
 [导入包](https://packaging.python.org/glossary/#term-import-package)