+++
title = "在ARM上编译"
date = 2019-12-25T04:56:50+10:00
weight = 27
chapter = false
+++

{{% notice info %}}
工作正在进行中
{{% /notice %}}

### 编译错误

如果您发现编译中止并出现以下错误：

```bash
✗ Ensuring frontend dependencies are up to date (This may take a while)
Error: signal: illegal instruction (core dumped)

✗ exit status 255
```

那么您将要检查 Nodejs 的版本是否与您的处理器兼容：

[nodejs/help#1453 (comment)](https://github.com/nodejs/help/issues/1453#issuecomment-415761791)

```bash
The Illegal instruction error concerns the production (by the JIT) of an instruction that the processor does not recognise as valid. That might arise if the architecture of what was downloaded does not match the real architecture of your machine (you are most likely to be on an x64).
```

要测试是否这是您遇到`非法指令`错误的原因，请运行：

    `node --v8-options`

然后看第二行。它应包含一个`ARM`标志， 例如:

`ARMv7=1 VFP3=0 VFP32DREGS=0 NEON=0 SUDIV=0`

这表明此版本的 Nodejs 是为 ARMv7 编译的。确保为您的处理器安装了正确版本的 Nodejs。
