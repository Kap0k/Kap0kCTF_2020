## Hard | SOS | Author: CrzZ

1. 伪加密

2. 使用合适的编码(UTF-8)打开发现这样的东西 ，可以看到上面那个meta是乱码的，所以可以猜测flag藏在那里

   `原网站用的Shift_JIS编码，但是下载下来自动转成了UTF8，所以正常打开全是乱码(`

   ![image-20201128204850875.png](https://i.loli.net/2020/12/07/f3U5viIaylP6NbJ.png)

3. 搜索SOS团，找到原网站：[SOS Dan web site (haruhi.tv)](http://www.haruhi.tv/)

4. 下载，将编码转成一致(UTF-8)，然后xor就会出来一串东西，其实就是那串meta的异或

   ```python
   import requests
   
   url = "http://www.haruhi.tv/"
   
   r = requests.get(url)
   orgData = r.text.encode('utf-8')
   with open('111.html', 'rb') as f:
       newData = f.read()
   for i in range(min(len(orgData), len(newData))):
       c = orgData[i] ^ newData[i]
       print(chr(c), end='')
   ```

   ![image-20201128210655908.png](https://i.loli.net/2020/12/07/ZbwQOM6A8vt4Pof.png)

   ```
   ....................!?!!.?....................?.?!.?................................!.?.......!?!!.?!!!!!!?.?!.?!!!!!...............!.?...............!?!!.?!!!!!!!!!!!!!!?.?!.?!!!!!!!!!!!.................!.?...........!?!!.?..........?.?!.?............!.?.........!?!!.?........?.?!.?....!.?.......!?!!.?!!!!!!?.?!.?!!!!!!!!!!!.?. 2BXc8oQKLvcBmzLxu4du34cD1kjiyvxa
   ```

5. 可以看到有两部分，前半部分是`.?!`组成，可以想到Ook!编码 [Brainfuck/Ook! Obfuscation/Encoding](https://www.splitbrain.org/services/ook)

   解码得到`tip:Base`

6. 根据tip找到Base一家子对后半部分使用CyberChef进行尝试得到flag

   >  b85decode(b58decode("2BXc8oQKLvcBmzLxu4du34cD1kjiyvxa")) == Kap0k{S05_forever}

   还可以直接用basecrack把flag跑出来

   ![image-20201207123422858.png](https://i.loli.net/2020/12/07/CjGnqBaJKUlIop9.png)

