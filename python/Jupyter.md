# Jupyter 使用

## 插件配置

```text
pip install jupyter_contrib_nbextensions
jupyter contrib nbextension install --user
```

- **Hinterland**：代码补全
- **Autopep8**： 美化代码



## 配置服务化

打开并修改配置文件 `vim ~/.jupyter/jupyter_notebook_config.py`

```json
{
   "NotebookApp": {
  	"ip": "*",
    "port": 8899,
    "open_browser": false
  }
}
```

