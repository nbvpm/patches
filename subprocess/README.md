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

Reason is, there are accented characters in the paths.

The solutions given in https://bugs.python.org/issue1759845 were not satisfying because:
  1. I don't want to apply a patch to each PC I got python 2.7 installed on (and with the high probabilty I forget to do that).
  2. I failed to install the correct subprocesww wheel (2 files) because I didn't (and still don't) know which one to pip.
  3. Even if I could manage to import subprocesww, I reckon it would have been of no help because my script don't use Popen.
  4. The traceback is pretty clear: the problem is in `list2cmdline`, not elsewhere.
  5. The `args` passed to subprocess function is always a list, otherwise I'd do the encoding beforehand.

Hence, monkey patching seems the best solution.

To use this script, put it in a folder and import subprocess from there.

Example:
<pre>
from .patches import subprocess
...
out = subprocess.check_output(...)
</pre>
I reckon this solution also works with the other functions (communicate, check_call... and possibly Popen) because it does with .check_output and .call.
