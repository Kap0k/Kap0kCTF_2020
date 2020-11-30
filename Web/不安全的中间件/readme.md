## Hard | 不安全的中间件 | Author: CrzZ

这题主要是让你们熟悉下php和学会渗透前的简单的信息收集工作

信息收集：

- 使用浏览器`Wappalyzer`插件得知后端是`PHP/7.3.4`，使用的中间件是`nginx/1.15.11`

  也可以在`Headers`里看到

  ![image-20201130163646732.png](https://i.loli.net/2020/11/30/wzFu65ZEPhnYUB9.png)

- 在url后面随便输入一些东西让它报错或者访问`index.html`，发现用的是phpstudy

  查看phpstudy的更新日志：[phpStudy v8.1（32+64位） 版本下载及更新日志 - 小皮面板(phpstudy)交流社区 (xp.cn)](https://www.xp.cn/wenda/401.html)

  然后找到这篇利用文章：[PhpStudy存在默认解析漏洞 · 语雀 (yuque.com)](https://www.yuque.com/u541354/lt8xzm/xerr95)

解题流程：

- [PHPFuck (splitline.github.io)](https://splitline.github.io/PHPFuck/)，生成`eval($_POST[0]);`的执行代码，把`php`和`空格`删掉
- base64之后post上去，然后在cookie里找PHPSESSID，md5之后就是文件名
- 利用phpStudy_v8.1.0.7默认配置的文件解析漏洞执行php代码
- 用[蚁剑]([AntSwordProject/AntSword-Loader: AntSword 加载器 (github.com)](https://github.com/AntSwordProject/AntSword-Loader))连上去在网站根目录找到flag

> `Kap0k{~php5tudy_1s_un54fe~}`

