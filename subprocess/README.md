I was converting a PDF file using ghostscript (C:\Program Files\gs\gs9.54.0\bin\gswin64c.exe) when I ran into this:
<pre>
Traceback (most recent call last):
  File "convert_pdf_to_txt.py", line 182, in <module>
    main()
  File "convert_pdf_to_txt.py", line 172, in main
    out = subprocess.check_output(cmd)
  File "E:\Python27\lib\subprocess.py", line 216, in check_output
    process = Popen(stdout=PIPE, *popenargs, **kwargs)
  File "E:\Python27\lib\subprocess.py", line 394, in __init__
    errread, errwrite)
  File "E:\Python27\lib\subprocess.py", line 599, in _execute_child
    args = list2cmdline(args)
  File "E:\Python27\lib\subprocess.py", line 294, in list2cmdline
    return ''.join(result)
UnicodeDecodeError: 'ascii' codec can't decode byte 0xe9 in position 0: ordinal not in range(128)
</pre>

Why? Paths contain accented characters.

The solutions given in https://bugs.python.org/issue1759845 were not satisfying for various reasons. And the traceback is pretty clear: the problem is in `list2cmdline`, not elsewhere.

Hence, monkey patching seems the best solution. One of them being that at some time we'll have to move up to 3.x (or 4.x).

To use this patch, put it at the top of your script.

Example:
<pre>
import subprocess
...
def list2cmdline_patched(seq):
    ...
subprocess.list2cmdline = list2cmdline_patched
...
subprocess.check_output(...)
</pre>
I reckon this solution also works with the other functions (communicate, check_call... and possibly Popen) because it does with .check_output and .call.
