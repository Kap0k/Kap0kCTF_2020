## Easy | Checker | Author: devoke

1. 配环境

   https://www.cnblogs.com/baiyuer/p/9606773.html

2. 找源码

   https://pypi.org/ 或 pip show kap0kchecker

3. 解异或

   [cyberchef ](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')XOR({'option':'Hex','string':'20'},'Standard',false)&input=MHg2YiwweDQxLDB4NTAsMHgxMCwweDRiLDB4NWIsMHg2YywKICAgICAgICAweDExLDB4NDYsMHgxMywweDdmLDB4MTEsMHg1MywweDdmLAogICAgICAgIDB4NTMsMHg0OCwweDEwLDB4NTIsMHgxNywweDdmLDB4NjksCiAgICAgICAgMHg3ZiwweDU1LDB4NTMsMHgxMywweDdmLDB4NzAsMHg1OSwKICAgICAgICAweDE3LDB4NDgsMHgxMCwweDRlLDB4NWQ)

   or

   ```python
   cipher = [0x6b,0x41,0x50,0x10,0x4b,0x5b,0x6c,
           0x11,0x46,0x13,0x7f,0x11,0x53,0x7f,
           0x53,0x48,0x10,0x52,0x17,0x7f,0x69,
           0x7f,0x55,0x53,0x13,0x7f,0x70,0x59,
           0x17,0x48,0x10,0x4e,0x5d]
   print("".join(chr(i^0x20) for i in cipher ))
   ```

4. 拿flag

   > Kap0k{L1f3_1s_sh0r7_I_us3_Py7h0n}