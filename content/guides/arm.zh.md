+++
title = "在ARM上编译"
date = 2019-12-25T04:56:50+10:00
weight = 27
chapter = false
+++

{{% notice info %}}
Work in progress
{{% /notice %}}

### Compiler Errors

If you find that compilation aborts with the following error:

```bash
✗ Ensuring frontend dependencies are up to date (This may take a while)
Error: signal: illegal instruction (core dumped)

✗ exit status 255
```
then you will want to check whether the version of node is compatible with your processor:

[nodejs/help#1453 (comment)](https://github.com/nodejs/help/issues/1453#issuecomment-415761791)

```bash
The Illegal instruction error concerns the production (by the JIT) of an instruction that the processor does not recognise as valid. That might arise if the architecture of what was downloaded does not match the real architecture of your machine (you are most likely to be on an x64).
```

To test whether this is the reason you are experiencing the 'Illegal instruction' error, run:

    `node --v8-options` 

and look at the second line. It should contain an `ARM` flag, EG:

`ARMv7=1 VFP3=0 VFP32DREGS=0 NEON=0 SUDIV=0`

This indicates that this version of node is compiled for ARMv7. Ensure the correct version of node is installed for your processor.


