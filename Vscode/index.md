# Vscode使用笔记

- [Vscode使用笔记](#vscode使用笔记)
  - [Vscode修改PYTHONPATH](#vscode修改pythonpath)

## Vscode修改PYTHONPATH
python 导入第三方包时的方式为:
- 1、`sys.path` 由 `PYTHONPATH` 环境变量初始化，在此基础上添加当前环境的一些默认变量。
- 2、将当前工作目录放入`sys.path` 。
- 3、从 `sys.path` 中寻找第三方包。
> [python.org](https://docs.python.org/3/library/sys.html?highlight=sys%20path#sys.path)

vscode `launch.json`中 `cwd` 关键字只改变文件 **debug** 运行时的**工作目录**，不会修改当前第三方包的搜寻目录。由于 **vscode** 默认不会将当前**工作区目录**放入`sys.path` 下， 因此当运行文件与第三方包不处于当前工作区顶层时，有时会存在找不到包的错误。
> 注意：此处 **pycharm** 等 IDE 可能不会遇见该问题，因为这些 IDE 可能会自动将当前工作区放入 `sys.path` , 可自行打印 `sys.path` 输出进行观察。

vscode 手动修改 `PYTHONPATH` 的方法：
- 1、通过 `.env` 文件可以设定当前 **debug** 运行态下的环境变量。 默认 vscode 会搜索 `"python.envFile": "${workspaceFolder}/.env"` 即当前工作区目录下的 `.env` 文件， 也可以修改 `setting.json` 中的 `python.envFile` 改变默认值。
- 2、 `launch.json` 可以直接通过 `envFile` 关键字指定 `envFile` 位置
- 3、通过 `launch.json` 中的 `env` 关键字可以直接定义环境变量
  
> 优先级顺序: 1 > 2 > 3

当通过`.env` 文件使用时，`PYTHONPATH` 会影响所有插件(**lint**)，但是不会影响在终端运行的工具(**debugging**)，使用 `terminal.integrated.env.*` 设定时反之。
