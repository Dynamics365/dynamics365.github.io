# AxBuild.exe for Parallel Compile on AOS


If you have only 10 mins to build AX, try this

In AOS server, go to `C:\Program Files\Microsoft Dynamics AX\60\Server\DAX\bin` and open `cmd` from here then run this command

```ps
axbuild.exe xppcompileall /s=01 /altbin="C:\Program Files (x86)\Microsoft Dynamics AX\60\Client\Bin"
```

`/s` is your AOS number instance, you can check it in windows services `/altbin` the path to AX client

![axbuild-exe-for-parallel-compile-on-aos_2](axbuild-exe-for-parallel-compile-on-aos_2.png "axbuild")

**Here is the result**
![axbuild-exe-for-parallel-compile-on-aos_2](axbuild-exe-for-parallel-compile-on-aos_3.png "axbuild")
Once compile complete, you can import compile log file at `C:\Program Files\Microsoft Dynamics AX\60\Server\DAX\log` into compiler output of AX client
![axbuild-exe-for-parallel-compile-on-aos_2](axbuild-exe-for-parallel-compile-on-aos_4.png "axbuild")

{{< admonition info "Reference" >}}
**MSDN**: https://msdn.microsoft.com/en-us/library/dn528954.aspx
{{< /admonition >}}
