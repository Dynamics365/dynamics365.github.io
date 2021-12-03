# Batch rename files to lowercase?


Go to the directory and run the following command:

`for /f "Tokens=*" %f in ('dir /l/b/a-d') do (rename "%f" "%f")`

for /f "Tokens=*" %%f in ('dir /l/b/a-d') do (rename "%%f" "%%f")

rename folders

`for /f "Tokens=*" %f in ('dir /l/b') do (rename "%f" "%f")`

Recursive version (added /r to dir)

`for /f "Tokens=*" %f in ('dir /l/b/a-d/s') do (rename "%f" "%f")`

