+++
date        = "2009-12-26T12:00:00-04:00"
title       = "Extending Python with modules written in C"
description = ""
tags        = [ "code", "c", "python" ]
topics      = [ "code" ]
slug        = "extending-python-with-modules-written-in-c"
+++

Using C (or C++) to create Python modules is really quite simple, providing you know a little C of course. I recently had to do some work around getting a bunch of legacy C code talking to a newer system and thought I'd post a nice simple example of how the Python extensions work.

This code gives you a single method "do()" that will print the output of a command, passed to it as a string, to stdout and return the exit code as a python int.

<!--more-->

Dump this into `mycmd.c`

```
#include <Python.h>

static PyObject * mycmd_do(PyObject *self, PyObject *args) {
    const char *command;
    int sts;

    if (!PyArg_ParseTuple(args, "s", &command))
        return NULL;
    sts = system(command);
    return Py_BuildValue("i", sts);
}

static PyMethodDef MyCmdMethods[] = {
    {"do", mycmd_do, METH_VARARGS, "Print output of 'cmd', return exit code."},
    {NULL, NULL, 0, NULL}        /* Sentinel */
};

PyMODINIT_FUNC
initmycmd(void) {
    (void) Py_InitModule("mycmd", MyCmdMethods);
}

int main(int argc, char *argv[]) {
    Py_SetProgramName(argv[0]);
    Py_Initialize();
    initmycmd();
    return 0;
}
```

Great, so we have some example code now, here is how you build an importable module with it:

```
greg@codemine:~/code/mycmd %> cc -dynamic -g -Wall -I/System/Library/Frameworks/Python.framework/Versions/2.6/include/python2.6 -c mycmd.c -o mycmd.o
greg@codemine:~/code/mycmd %> cc -bundle -undefined dynamic_lookup mycmd.o -o mycmd.so
```

<strong>Note:</strong> Don't forget to replace the include path above with the correct path to Python.h on your machine.

This should give you a mycmd.so on unix / linux and a mycmd.dll on windows. In the same directory, run a python interpreter and test it out.

```
greg@codemine:~/code/mycmd %> python
Python 2.6.3 (r263:75183, Nov  4 2009, 12:53:19)
[GCC 4.2.1 (Apple Inc. build 5646)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import mycmd
>>> mycmd.do('/usr/bin/false')
256
>>> mycmd.do('/usr/bin/true')
0
>>> mycmd.do('uname -a')
Darwin codemine.codelounge.int 10.2.0 Darwin Kernel Version 10.2.0: Tue Nov  3 10:37:10 PST 2009; root:xnu-1486.2.11~1/RELEASE_I386 i386
0
>>>
```

There is much more you can do around this, thankfully the <a href="http://docs.python.org/extending/">documentation</a> is remarkably good.

There is not much to the actual code. First, we define the C function that will handle our command "mycmd_do". Then we set up an array of methods we want to expose to python "MyCmdMethods". We then setup an initializer "initmycmd" to expose the module which is executed from "main" after the python initializer "Py_Initialize".
