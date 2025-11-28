# [`sys`](https://docs.python.org/3/library/sys.html#module-sys "sys: Access system-specific parameters and functions.") — System-specific parameters and functions[](https://docs.python.org/3/library/sys.html#module-sys "Permalink to this headline")

---

This module provides access to some variables used or maintained by the interpreter and to functions that interact strongly with the interpreter. It is always available.

sys.abiflags[](https://docs.python.org/3/library/sys.html#sys.abiflags "Permalink to this definition")

On POSIX systems where Python was built with the standard `configure` script, this contains the ABI flags as specified by [**PEP 3149**](https://peps.python.org/pep-3149/).

Changed in version 3.8: Default flags became an empty string (`m` flag for pymalloc has been removed).

New in version 3.2.

sys.addaudithook(_hook_)[](https://docs.python.org/3/library/sys.html#sys.addaudithook "Permalink to this definition")

Append the callable _hook_ to the list of active auditing hooks for the current (sub)interpreter.

When an auditing event is raised through the [`sys.audit()`](https://docs.python.org/3/library/sys.html#sys.audit "sys.audit") function, each hook will be called in the order it was added with the event name and the tuple of arguments. Native hooks added by [`PySys_AddAuditHook()`](https://docs.python.org/3/c-api/sys.html#c.PySys_AddAuditHook "PySys_AddAuditHook") are called first, followed by hooks added in the current (sub)interpreter. Hooks can then log the event, raise an exception to abort the operation, or terminate the process entirely.

Note that audit hooks are primarily for collecting information about internal or otherwise unobservable actions, whether by Python or libraries written in Python. They are not suitable for implementing a “sandbox”. In particular, malicious code can trivially disable or bypass hooks added using this function. At a minimum, any security-sensitive hooks must be added using the C API [`PySys_AddAuditHook()`](https://docs.python.org/3/c-api/sys.html#c.PySys_AddAuditHook "PySys_AddAuditHook") before initialising the runtime, and any modules allowing arbitrary memory modification (such as [`ctypes`](https://docs.python.org/3/library/ctypes.html#module-ctypes "ctypes: A foreign function library for Python.")) should be completely removed or closely monitored.

Calling [`sys.addaudithook()`](https://docs.python.org/3/library/sys.html#sys.addaudithook "sys.addaudithook") will itself raise an auditing event named `sys.addaudithook` with no arguments. If any existing hooks raise an exception derived from [`RuntimeError`](https://docs.python.org/3/library/exceptions.html#RuntimeError "RuntimeError"), the new hook will not be added and the exception suppressed. As a result, callers cannot assume that their hook has been added unless they control all existing hooks.

See the [audit events table](https://docs.python.org/3/library/audit_events.html#audit-events) for all events raised by CPython, and [**PEP 578**](https://peps.python.org/pep-0578/) for the original design discussion.

New in version 3.8.

Changed in version 3.8.1: Exceptions derived from [`Exception`](https://docs.python.org/3/library/exceptions.html#Exception "Exception") but not [`RuntimeError`](https://docs.python.org/3/library/exceptions.html#RuntimeError "RuntimeError") are no longer suppressed.

**CPython implementation detail:** When tracing is enabled (see [`settrace()`](https://docs.python.org/3/library/sys.html#sys.settrace "sys.settrace")), Python hooks are only traced if the callable has a `__cantrace__` member that is set to a true value. Otherwise, trace functions will skip the hook.

sys.argv[](https://docs.python.org/3/library/sys.html#sys.argv "Permalink to this definition")

The list of command line arguments passed to a Python script. `argv[0]` is the script name (it is operating system dependent whether this is a full pathname or not). If the command was executed using the [`-c`](https://docs.python.org/3/using/cmdline.html#cmdoption-c) command line option to the interpreter, `argv[0]` is set to the string `'-c'`. If no script name was passed to the Python interpreter, `argv[0]` is the empty string.

To loop over the standard input, or the list of files given on the command line, see the [`fileinput`](https://docs.python.org/3/library/fileinput.html#module-fileinput "fileinput: Loop over standard input or a list of files.") module.

See also [`sys.orig_argv`](https://docs.python.org/3/library/sys.html#sys.orig_argv "sys.orig_argv").

Note

 

On Unix, command line arguments are passed by bytes from OS. Python decodes them with filesystem encoding and “surrogateescape” error handler. When you need original bytes, you can get it by `[os.fsencode(arg) for arg in sys.argv]`.

sys.audit(_event_, _*args_)[](https://docs.python.org/3/library/sys.html#sys.audit "Permalink to this definition")

Raise an auditing event and trigger any active auditing hooks. _event_ is a string identifying the event, and _args_ may contain optional arguments with more information about the event. The number and types of arguments for a given event are considered a public and stable API and should not be modified between releases.

For example, one auditing event is named `os.chdir`. This event has one argument called _path_ that will contain the requested new working directory.

[`sys.audit()`](https://docs.python.org/3/library/sys.html#sys.audit "sys.audit") will call the existing auditing hooks, passing the event name and arguments, and will re-raise the first exception from any hook. In general, if an exception is raised, it should not be handled and the process should be terminated as quickly as possible. This allows hook implementations to decide how to respond to particular events: they can merely log the event or abort the operation by raising an exception.

Hooks are added using the [`sys.addaudithook()`](https://docs.python.org/3/library/sys.html#sys.addaudithook "sys.addaudithook") or [`PySys_AddAuditHook()`](https://docs.python.org/3/c-api/sys.html#c.PySys_AddAuditHook "PySys_AddAuditHook") functions.

The native equivalent of this function is [`PySys_Audit()`](https://docs.python.org/3/c-api/sys.html#c.PySys_Audit "PySys_Audit"). Using the native function is preferred when possible.

See the [audit events table](https://docs.python.org/3/library/audit_events.html#audit-events) for all events raised by CPython.

New in version 3.8.

sys.base_exec_prefix[](https://docs.python.org/3/library/sys.html#sys.base_exec_prefix "Permalink to this definition")

Set during Python startup, before `site.py` is run, to the same value as [`exec_prefix`](https://docs.python.org/3/library/sys.html#sys.exec_prefix "sys.exec_prefix"). If not running in a [virtual environment](https://docs.python.org/3/library/venv.html#venv-def), the values will stay the same; if `site.py` finds that a virtual environment is in use, the values of [`prefix`](https://docs.python.org/3/library/sys.html#sys.prefix "sys.prefix") and [`exec_prefix`](https://docs.python.org/3/library/sys.html#sys.exec_prefix "sys.exec_prefix") will be changed to point to the virtual environment, whereas [`base_prefix`](https://docs.python.org/3/library/sys.html#sys.base_prefix "sys.base_prefix") and [`base_exec_prefix`](https://docs.python.org/3/library/sys.html#sys.base_exec_prefix "sys.base_exec_prefix") will remain pointing to the base Python installation (the one which the virtual environment was created from).

New in version 3.3.

sys.base_prefix[](https://docs.python.org/3/library/sys.html#sys.base_prefix "Permalink to this definition")

Set during Python startup, before `site.py` is run, to the same value as [`prefix`](https://docs.python.org/3/library/sys.html#sys.prefix "sys.prefix"). If not running in a [virtual environment](https://docs.python.org/3/library/venv.html#venv-def), the values will stay the same; if `site.py` finds that a virtual environment is in use, the values of [`prefix`](https://docs.python.org/3/library/sys.html#sys.prefix "sys.prefix") and [`exec_prefix`](https://docs.python.org/3/library/sys.html#sys.exec_prefix "sys.exec_prefix") will be changed to point to the virtual environment, whereas [`base_prefix`](https://docs.python.org/3/library/sys.html#sys.base_prefix "sys.base_prefix") and [`base_exec_prefix`](https://docs.python.org/3/library/sys.html#sys.base_exec_prefix "sys.base_exec_prefix") will remain pointing to the base Python installation (the one which the virtual environment was created from).

New in version 3.3.

sys.byteorder[](https://docs.python.org/3/library/sys.html#sys.byteorder "Permalink to this definition")

An indicator of the native byte order. This will have the value `'big'` on big-endian (most-significant byte first) platforms, and `'little'` on little-endian (least-significant byte first) platforms.

sys.builtin_module_names[](https://docs.python.org/3/library/sys.html#sys.builtin_module_names "Permalink to this definition")

A tuple of strings containing the names of all modules that are compiled into this Python interpreter. (This information is not available in any other way — `modules.keys()` only lists the imported modules.)

See also the [`sys.stdlib_module_names`](https://docs.python.org/3/library/sys.html#sys.stdlib_module_names "sys.stdlib_module_names") list.

sys.call_tracing(_func_, _args_)[](https://docs.python.org/3/library/sys.html#sys.call_tracing "Permalink to this definition")

Call `func(*args)`, while tracing is enabled. The tracing state is saved, and restored afterwards. This is intended to be called from a debugger from a checkpoint, to recursively debug or profile some other code.

Tracing is suspended while calling a tracing function set by [`settrace()`](https://docs.python.org/3/library/sys.html#sys.settrace "sys.settrace") or [`setprofile()`](https://docs.python.org/3/library/sys.html#sys.setprofile "sys.setprofile") to avoid infinite recursion. `call_tracing()` enables explicit recursion of the tracing function.

sys.copyright[](https://docs.python.org/3/library/sys.html#sys.copyright "Permalink to this definition")

A string containing the copyright pertaining to the Python interpreter.

sys._clear_type_cache()[](https://docs.python.org/3/library/sys.html#sys._clear_type_cache "Permalink to this definition")

Clear the internal type cache. The type cache is used to speed up attribute and method lookups. Use the function _only_ to drop unnecessary references during reference leak debugging.

This function should be used for internal and specialized purposes only.

sys._current_frames()[](https://docs.python.org/3/library/sys.html#sys._current_frames "Permalink to this definition")

Return a dictionary mapping each thread’s identifier to the topmost stack frame currently active in that thread at the time the function is called. Note that functions in the [`traceback`](https://docs.python.org/3/library/traceback.html#module-traceback "traceback: Print or retrieve a stack traceback.") module can build the call stack given such a frame.

This is most useful for debugging deadlock: this function does not require the deadlocked threads’ cooperation, and such threads’ call stacks are frozen for as long as they remain deadlocked. The frame returned for a non-deadlocked thread may bear no relationship to that thread’s current activity by the time calling code examines the frame.

This function should be used for internal and specialized purposes only.

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `sys._current_frames` with no arguments.

sys._current_exceptions()[](https://docs.python.org/3/library/sys.html#sys._current_exceptions "Permalink to this definition")

Return a dictionary mapping each thread’s identifier to the topmost exception currently active in that thread at the time the function is called. If a thread is not currently handling an exception, it is not included in the result dictionary.

This is most useful for statistical profiling.

This function should be used for internal and specialized purposes only.

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `sys._current_exceptions` with no arguments.

Changed in version 3.12: Each value in the dictionary is now a single exception instance, rather than a 3-tuple as returned from `sys.exc_info()`.

sys.breakpointhook()[](https://docs.python.org/3/library/sys.html#sys.breakpointhook "Permalink to this definition")

This hook function is called by built-in [`breakpoint()`](https://docs.python.org/3/library/functions.html#breakpoint "breakpoint"). By default, it drops you into the [`pdb`](https://docs.python.org/3/library/pdb.html#module-pdb "pdb: The Python debugger for interactive interpreters.") debugger, but it can be set to any other function so that you can choose which debugger gets used.

The signature of this function is dependent on what it calls. For example, the default binding (e.g. `pdb.set_trace()`) expects no arguments, but you might bind it to a function that expects additional arguments (positional and/or keyword). The built-in `breakpoint()` function passes its `*args` and `**kws` straight through. Whatever `breakpointhooks()` returns is returned from `breakpoint()`.

The default implementation first consults the environment variable [`PYTHONBREAKPOINT`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONBREAKPOINT). If that is set to `"0"` then this function returns immediately; i.e. it is a no-op. If the environment variable is not set, or is set to the empty string, `pdb.set_trace()` is called. Otherwise this variable should name a function to run, using Python’s dotted-import nomenclature, e.g. `package.subpackage.module.function`. In this case, `package.subpackage.module` would be imported and the resulting module must have a callable named `function()`. This is run, passing in `*args` and `**kws`, and whatever `function()` returns, `sys.breakpointhook()` returns to the built-in [`breakpoint()`](https://docs.python.org/3/library/functions.html#breakpoint "breakpoint") function.

Note that if anything goes wrong while importing the callable named by [`PYTHONBREAKPOINT`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONBREAKPOINT), a [`RuntimeWarning`](https://docs.python.org/3/library/exceptions.html#RuntimeWarning "RuntimeWarning") is reported and the breakpoint is ignored.

Also note that if `sys.breakpointhook()` is overridden programmatically, [`PYTHONBREAKPOINT`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONBREAKPOINT) is _not_ consulted.

New in version 3.7.

sys._debugmallocstats()[](https://docs.python.org/3/library/sys.html#sys._debugmallocstats "Permalink to this definition")

Print low-level information to stderr about the state of CPython’s memory allocator.

If Python is [built in debug mode](https://docs.python.org/3/using/configure.html#debug-build) ([`configure --with-pydebug option`](https://docs.python.org/3/using/configure.html#cmdoption-with-pydebug)), it also performs some expensive internal consistency checks.

New in version 3.3.

**CPython implementation detail:** This function is specific to CPython. The exact output format is not defined here, and may change.

sys.dllhandle[](https://docs.python.org/3/library/sys.html#sys.dllhandle "Permalink to this definition")

Integer specifying the handle of the Python DLL.

[Availability](https://docs.python.org/3/library/intro.html#availability): Windows.

sys.displayhook(_value_)[](https://docs.python.org/3/library/sys.html#sys.displayhook "Permalink to this definition")

If _value_ is not `None`, this function prints `repr(value)` to `sys.stdout`, and saves _value_ in `builtins._`. If `repr(value)` is not encodable to `sys.stdout.encoding` with `sys.stdout.errors` error handler (which is probably `'strict'`), encode it to `sys.stdout.encoding` with `'backslashreplace'` error handler.

`sys.displayhook` is called on the result of evaluating an [expression](https://docs.python.org/3/glossary.html#term-expression) entered in an interactive Python session. The display of these values can be customized by assigning another one-argument function to `sys.displayhook`.

Pseudo-code:

def displayhook(value):
    if value is None:
        return
    # Set '_' to None to avoid recursion
    builtins._ = None
    text = repr(value)
    try:
        sys.stdout.write(text)
    except UnicodeEncodeError:
        bytes = text.encode(sys.stdout.encoding, 'backslashreplace')
        if hasattr(sys.stdout, 'buffer'):
            sys.stdout.buffer.write(bytes)
        else:
            text = bytes.decode(sys.stdout.encoding, 'strict')
            sys.stdout.write(text)
    sys.stdout.write("\n")
    builtins._ = value

Changed in version 3.2: Use `'backslashreplace'` error handler on [`UnicodeEncodeError`](https://docs.python.org/3/library/exceptions.html#UnicodeEncodeError "UnicodeEncodeError").

sys.dont_write_bytecode[](https://docs.python.org/3/library/sys.html#sys.dont_write_bytecode "Permalink to this definition")

If this is true, Python won’t try to write `.pyc` files on the import of source modules. This value is initially set to `True` or `False` depending on the [`-B`](https://docs.python.org/3/using/cmdline.html#cmdoption-B) command line option and the [`PYTHONDONTWRITEBYTECODE`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONDONTWRITEBYTECODE) environment variable, but you can set it yourself to control bytecode file generation.

sys._emscripten_info[](https://docs.python.org/3/library/sys.html#sys._emscripten_info "Permalink to this definition")

A [named tuple](https://docs.python.org/3/glossary.html#term-named-tuple) holding information about the environment on the _wasm32-emscripten_ platform. The named tuple is provisional and may change in the future.

_emscripten_info.emscripten_version[](https://docs.python.org/3/library/sys.html#sys._emscripten_info.emscripten_version "Permalink to this definition")

Emscripten version as tuple of ints (major, minor, micro), e.g. `(3, 1, 8)`.

_emscripten_info.runtime[](https://docs.python.org/3/library/sys.html#sys._emscripten_info.runtime "Permalink to this definition")

Runtime string, e.g. browser user agent, `'Node.js v14.18.2'`, or `'UNKNOWN'`.

_emscripten_info.pthreads[](https://docs.python.org/3/library/sys.html#sys._emscripten_info.pthreads "Permalink to this definition")

`True` if Python is compiled with Emscripten pthreads support.

_emscripten_info.shared_memory[](https://docs.python.org/3/library/sys.html#sys._emscripten_info.shared_memory "Permalink to this definition")

`True` if Python is compiled with shared memory support.

[Availability](https://docs.python.org/3/library/intro.html#availability): Emscripten.

New in version 3.11.

sys.pycache_prefix[](https://docs.python.org/3/library/sys.html#sys.pycache_prefix "Permalink to this definition")

If this is set (not `None`), Python will write bytecode-cache `.pyc` files to (and read them from) a parallel directory tree rooted at this directory, rather than from `__pycache__` directories in the source code tree. Any `__pycache__` directories in the source code tree will be ignored and new `.pyc` files written within the pycache prefix. Thus if you use [`compileall`](https://docs.python.org/3/library/compileall.html#module-compileall "compileall: Tools for byte-compiling all Python source files in a directory tree.") as a pre-build step, you must ensure you run it with the same pycache prefix (if any) that you will use at runtime.

A relative path is interpreted relative to the current working directory.

This value is initially set based on the value of the [`-X`](https://docs.python.org/3/using/cmdline.html#cmdoption-X) `pycache_prefix=PATH` command-line option or the [`PYTHONPYCACHEPREFIX`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPYCACHEPREFIX) environment variable (command-line takes precedence). If neither are set, it is `None`.

New in version 3.8.

sys.excepthook(_type_, _value_, _traceback_)[](https://docs.python.org/3/library/sys.html#sys.excepthook "Permalink to this definition")

This function prints out a given traceback and exception to `sys.stderr`.

When an exception other than [`SystemExit`](https://docs.python.org/3/library/exceptions.html#SystemExit "SystemExit") is raised and uncaught, the interpreter calls `sys.excepthook` with three arguments, the exception class, exception instance, and a traceback object. In an interactive session this happens just before control is returned to the prompt; in a Python program this happens just before the program exits. The handling of such top-level exceptions can be customized by assigning another three-argument function to `sys.excepthook`.

Raise an auditing event `sys.excepthook` with arguments `hook`, `type`, `value`, `traceback` when an uncaught exception occurs. If no hook has been set, `hook` may be `None`. If any hook raises an exception derived from [`RuntimeError`](https://docs.python.org/3/library/exceptions.html#RuntimeError "RuntimeError") the call to the hook will be suppressed. Otherwise, the audit hook exception will be reported as unraisable and `sys.excepthook` will be called.

See also

 

The [`sys.unraisablehook()`](https://docs.python.org/3/library/sys.html#sys.unraisablehook "sys.unraisablehook") function handles unraisable exceptions and the [`threading.excepthook()`](https://docs.python.org/3/library/threading.html#threading.excepthook "threading.excepthook") function handles exception raised by [`threading.Thread.run()`](https://docs.python.org/3/library/threading.html#threading.Thread.run "threading.Thread.run").

sys.__breakpointhook__[](https://docs.python.org/3/library/sys.html#sys.__breakpointhook__ "Permalink to this definition")

sys.__displayhook__[](https://docs.python.org/3/library/sys.html#sys.__displayhook__ "Permalink to this definition")

sys.__excepthook__[](https://docs.python.org/3/library/sys.html#sys.__excepthook__ "Permalink to this definition")

sys.__unraisablehook__[](https://docs.python.org/3/library/sys.html#sys.__unraisablehook__ "Permalink to this definition")

These objects contain the original values of `breakpointhook`, `displayhook`, `excepthook`, and `unraisablehook` at the start of the program. They are saved so that `breakpointhook`, `displayhook` and `excepthook`, `unraisablehook` can be restored in case they happen to get replaced with broken or alternative objects.

New in version 3.7: __breakpointhook__

New in version 3.8: __unraisablehook__

sys.exception()[](https://docs.python.org/3/library/sys.html#sys.exception "Permalink to this definition")

This function, when called while an exception handler is executing (such as an `except` or `except*` clause), returns the exception instance that was caught by this handler. When exception handlers are nested within one another, only the exception handled by the innermost handler is accessible.

If no exception handler is executing, this function returns `None`.

New in version 3.11.

sys.exc_info()[](https://docs.python.org/3/library/sys.html#sys.exc_info "Permalink to this definition")

This function returns the old-style representation of the handled exception. If an exception `e` is currently handled (so [`exception()`](https://docs.python.org/3/library/sys.html#sys.exception "sys.exception") would return `e`), [`exc_info()`](https://docs.python.org/3/library/sys.html#sys.exc_info "sys.exc_info") returns the tuple `(type(e), e, e.__traceback__)`. That is, a tuple containing the type of the exception (a subclass of [`BaseException`](https://docs.python.org/3/library/exceptions.html#BaseException "BaseException")), the exception itself, and a [traceback object](https://docs.python.org/3/reference/datamodel.html#traceback-objects) which typically encapsulates the call stack at the point where the exception last occurred.

If no exception is being handled anywhere on the stack, this function return a tuple containing three `None` values.

Changed in version 3.11: The `type` and `traceback` fields are now derived from the `value` (the exception instance), so when an exception is modified while it is being handled, the changes are reflected in the results of subsequent calls to [`exc_info()`](https://docs.python.org/3/library/sys.html#sys.exc_info "sys.exc_info").

sys.exec_prefix[](https://docs.python.org/3/library/sys.html#sys.exec_prefix "Permalink to this definition")

A string giving the site-specific directory prefix where the platform-dependent Python files are installed; by default, this is also `'/usr/local'`. This can be set at build time with the `--exec-prefix` argument to the **configure** script. Specifically, all configuration files (e.g. the `pyconfig.h` header file) are installed in the directory `_exec_prefix_/lib/python_X.Y_/config`, and shared library modules are installed in `_exec_prefix_/lib/python_X.Y_/lib-dynload`, where _X.Y_ is the version number of Python, for example `3.2`.

Note

 

If a [virtual environment](https://docs.python.org/3/library/venv.html#venv-def) is in effect, this value will be changed in `site.py` to point to the virtual environment. The value for the Python installation will still be available, via [`base_exec_prefix`](https://docs.python.org/3/library/sys.html#sys.base_exec_prefix "sys.base_exec_prefix").

sys.executable[](https://docs.python.org/3/library/sys.html#sys.executable "Permalink to this definition")

A string giving the absolute path of the executable binary for the Python interpreter, on systems where this makes sense. If Python is unable to retrieve the real path to its executable, [`sys.executable`](https://docs.python.org/3/library/sys.html#sys.executable "sys.executable") will be an empty string or `None`.

sys.exit([_arg_])[](https://docs.python.org/3/library/sys.html#sys.exit "Permalink to this definition")

Raise a [`SystemExit`](https://docs.python.org/3/library/exceptions.html#SystemExit "SystemExit") exception, signaling an intention to exit the interpreter.

The optional argument _arg_ can be an integer giving the exit status (defaulting to zero), or another type of object. If it is an integer, zero is considered “successful termination” and any nonzero value is considered “abnormal termination” by shells and the like. Most systems require it to be in the range 0–127, and produce undefined results otherwise. Some systems have a convention for assigning specific meanings to specific exit codes, but these are generally underdeveloped; Unix programs generally use 2 for command line syntax errors and 1 for all other kind of errors. If another type of object is passed, `None` is equivalent to passing zero, and any other object is printed to [`stderr`](https://docs.python.org/3/library/sys.html#sys.stderr "sys.stderr") and results in an exit code of 1. In particular, `sys.exit("some error message")` is a quick way to exit a program when an error occurs.

Since [`exit()`](https://docs.python.org/3/library/constants.html#exit "exit") ultimately “only” raises an exception, it will only exit the process when called from the main thread, and the exception is not intercepted. Cleanup actions specified by finally clauses of [`try`](https://docs.python.org/3/reference/compound_stmts.html#try) statements are honored, and it is possible to intercept the exit attempt at an outer level.

Changed in version 3.6: If an error occurs in the cleanup after the Python interpreter has caught [`SystemExit`](https://docs.python.org/3/library/exceptions.html#SystemExit "SystemExit") (such as an error flushing buffered data in the standard streams), the exit status is changed to 120.

sys.flags[](https://docs.python.org/3/library/sys.html#sys.flags "Permalink to this definition")

The [named tuple](https://docs.python.org/3/glossary.html#term-named-tuple) _flags_ exposes the status of command line flags. The attributes are read only.

|   |   |
|---|---|
|flags.debug[](https://docs.python.org/3/library/sys.html#sys.flags.debug "Permalink to this definition")|[`-d`](https://docs.python.org/3/using/cmdline.html#cmdoption-d)|
|flags.inspect[](https://docs.python.org/3/library/sys.html#sys.flags.inspect "Permalink to this definition")|[`-i`](https://docs.python.org/3/using/cmdline.html#cmdoption-i)|
|flags.interactive[](https://docs.python.org/3/library/sys.html#sys.flags.interactive "Permalink to this definition")|[`-i`](https://docs.python.org/3/using/cmdline.html#cmdoption-i)|
|flags.isolated[](https://docs.python.org/3/library/sys.html#sys.flags.isolated "Permalink to this definition")|[`-I`](https://docs.python.org/3/using/cmdline.html#cmdoption-I)|
|flags.optimize[](https://docs.python.org/3/library/sys.html#sys.flags.optimize "Permalink to this definition")|[`-O`](https://docs.python.org/3/using/cmdline.html#cmdoption-O) or [`-OO`](https://docs.python.org/3/using/cmdline.html#cmdoption-OO)|
|flags.dont_write_bytecode[](https://docs.python.org/3/library/sys.html#sys.flags.dont_write_bytecode "Permalink to this definition")|[`-B`](https://docs.python.org/3/using/cmdline.html#cmdoption-B)|
|flags.no_user_site[](https://docs.python.org/3/library/sys.html#sys.flags.no_user_site "Permalink to this definition")|[`-s`](https://docs.python.org/3/using/cmdline.html#cmdoption-s)|
|flags.no_site[](https://docs.python.org/3/library/sys.html#sys.flags.no_site "Permalink to this definition")|[`-S`](https://docs.python.org/3/using/cmdline.html#cmdoption-S)|
|flags.ignore_environment[](https://docs.python.org/3/library/sys.html#sys.flags.ignore_environment "Permalink to this definition")|[`-E`](https://docs.python.org/3/using/cmdline.html#cmdoption-E)|
|flags.verbose[](https://docs.python.org/3/library/sys.html#sys.flags.verbose "Permalink to this definition")|[`-v`](https://docs.python.org/3/using/cmdline.html#cmdoption-1)|
|flags.bytes_warning[](https://docs.python.org/3/library/sys.html#sys.flags.bytes_warning "Permalink to this definition")|[`-b`](https://docs.python.org/3/using/cmdline.html#cmdoption-b)|
|flags.quiet[](https://docs.python.org/3/library/sys.html#sys.flags.quiet "Permalink to this definition")|[`-q`](https://docs.python.org/3/using/cmdline.html#cmdoption-q)|
|flags.hash_randomization[](https://docs.python.org/3/library/sys.html#sys.flags.hash_randomization "Permalink to this definition")|[`-R`](https://docs.python.org/3/using/cmdline.html#cmdoption-R)|
|flags.dev_mode[](https://docs.python.org/3/library/sys.html#sys.flags.dev_mode "Permalink to this definition")|[`-X dev`](https://docs.python.org/3/using/cmdline.html#cmdoption-X) ([Python Development Mode](https://docs.python.org/3/library/devmode.html#devmode))|
|flags.utf8_mode[](https://docs.python.org/3/library/sys.html#sys.flags.utf8_mode "Permalink to this definition")|[`-X utf8`](https://docs.python.org/3/using/cmdline.html#cmdoption-X)|
|flags.safe_path[](https://docs.python.org/3/library/sys.html#sys.flags.safe_path "Permalink to this definition")|[`-P`](https://docs.python.org/3/using/cmdline.html#cmdoption-P)|
|flags.int_max_str_digits[](https://docs.python.org/3/library/sys.html#sys.flags.int_max_str_digits "Permalink to this definition")|[`-X int_max_str_digits`](https://docs.python.org/3/using/cmdline.html#cmdoption-X) ([integer string conversion length limitation](https://docs.python.org/3/library/stdtypes.html#int-max-str-digits))|
|flags.warn_default_encoding[](https://docs.python.org/3/library/sys.html#sys.flags.warn_default_encoding "Permalink to this definition")|[`-X warn_default_encoding`](https://docs.python.org/3/using/cmdline.html#cmdoption-X)|

Changed in version 3.2: Added `quiet` attribute for the new [`-q`](https://docs.python.org/3/using/cmdline.html#cmdoption-q) flag.

New in version 3.2.3: The `hash_randomization` attribute.

Changed in version 3.3: Removed obsolete `division_warning` attribute.

Changed in version 3.4: Added `isolated` attribute for [`-I`](https://docs.python.org/3/using/cmdline.html#cmdoption-I) `isolated` flag.

Changed in version 3.7: Added the `dev_mode` attribute for the new [Python Development Mode](https://docs.python.org/3/library/devmode.html#devmode) and the `utf8_mode` attribute for the new [`-X`](https://docs.python.org/3/using/cmdline.html#cmdoption-X) `utf8` flag.

Changed in version 3.10: Added `warn_default_encoding` attribute for [`-X`](https://docs.python.org/3/using/cmdline.html#cmdoption-X) `warn_default_encoding` flag.

Changed in version 3.11: Added the `safe_path` attribute for [`-P`](https://docs.python.org/3/using/cmdline.html#cmdoption-P) option.

Changed in version 3.11: Added the `int_max_str_digits` attribute.

sys.float_info[](https://docs.python.org/3/library/sys.html#sys.float_info "Permalink to this definition")

A [named tuple](https://docs.python.org/3/glossary.html#term-named-tuple) holding information about the float type. It contains low level information about the precision and internal representation. The values correspond to the various floating-point constants defined in the standard header file `float.h` for the ‘C’ programming language; see section 5.2.4.2.2 of the 1999 ISO/IEC C standard [[C99]](https://docs.python.org/3/library/sys.html#c99), ‘Characteristics of floating types’, for details.

Attributes of the `float_info` [named tuple](https://docs.python.org/3/glossary.html#term-named-tuple)[](https://docs.python.org/3/library/sys.html#id2 "Permalink to this table")
|attribute|float.h macro|explanation|
|---|---|---|
|float_info.epsilon[](https://docs.python.org/3/library/sys.html#sys.float_info.epsilon "Permalink to this definition")|`DBL_EPSILON`|difference between 1.0 and the least value greater than 1.0 that is representable as a float.<br><br>See also [`math.ulp()`](https://docs.python.org/3/library/math.html#math.ulp "math.ulp").|
|float_info.dig[](https://docs.python.org/3/library/sys.html#sys.float_info.dig "Permalink to this definition")|`DBL_DIG`|The maximum number of decimal digits that can be faithfully represented in a float; see below.|
|float_info.mant_dig[](https://docs.python.org/3/library/sys.html#sys.float_info.mant_dig "Permalink to this definition")|`DBL_MANT_DIG`|Float precision: the number of base-`radix` digits in the significand of a float.|
|float_info.max[](https://docs.python.org/3/library/sys.html#sys.float_info.max "Permalink to this definition")|`DBL_MAX`|The maximum representable positive finite float.|
|float_info.max_exp[](https://docs.python.org/3/library/sys.html#sys.float_info.max_exp "Permalink to this definition")|`DBL_MAX_EXP`|The maximum integer _e_ such that `radix**(e-1)` is a representable finite float.|
|float_info.max_10_exp[](https://docs.python.org/3/library/sys.html#sys.float_info.max_10_exp "Permalink to this definition")|`DBL_MAX_10_EXP`|The maximum integer _e_ such that `10**e` is in the range of representable finite floats.|
|float_info.min[](https://docs.python.org/3/library/sys.html#sys.float_info.min "Permalink to this definition")|`DBL_MIN`|The minimum representable positive _normalized_ float.<br><br>Use [`math.ulp(0.0)`](https://docs.python.org/3/library/math.html#math.ulp "math.ulp") to get the smallest positive _denormalized_ representable float.|
|float_info.min_exp[](https://docs.python.org/3/library/sys.html#sys.float_info.min_exp "Permalink to this definition")|`DBL_MIN_EXP`|The minimum integer _e_ such that `radix**(e-1)` is a normalized float.|
|float_info.min_10_exp[](https://docs.python.org/3/library/sys.html#sys.float_info.min_10_exp "Permalink to this definition")|`DBL_MIN_10_EXP`|The minimum integer _e_ such that `10**e` is a normalized float.|
|float_info.radix[](https://docs.python.org/3/library/sys.html#sys.float_info.radix "Permalink to this definition")|`FLT_RADIX`|The radix of exponent representation.|
|float_info.rounds[](https://docs.python.org/3/library/sys.html#sys.float_info.rounds "Permalink to this definition")|`FLT_ROUNDS`|An integer representing the rounding mode for floating-point arithmetic. This reflects the value of the system `FLT_ROUNDS` macro at interpreter startup time:<br><br>- `-1`: indeterminable<br>    <br>- `0`: toward zero<br>    <br>- `1`: to nearest<br>    <br>- `2`: toward positive infinity<br>    <br>- `3`: toward negative infinity<br>    <br><br>All other values for `FLT_ROUNDS` characterize implementation-defined rounding behavior.|

The attribute [`sys.float_info.dig`](https://docs.python.org/3/library/sys.html#sys.float_info.dig "sys.float_info.dig") needs further explanation. If `s` is any string representing a decimal number with at most `sys.float_info.dig` significant digits, then converting `s` to a float and back again will recover a string representing the same decimal value:

>>>

>>> import sys
>>> sys.float_info.dig
15
>>> s = '3.14159265358979'    # decimal string with 15 significant digits
>>> format(float(s), '.15g')  # convert to float and back -> same value
'3.14159265358979'

But for strings with more than [`sys.float_info.dig`](https://docs.python.org/3/library/sys.html#sys.float_info.dig "sys.float_info.dig") significant digits, this isn’t always true:

>>>

>>> s = '9876543211234567'    # 16 significant digits is too many!
>>> format(float(s), '.16g')  # conversion changes value
'9876543211234568'

sys.float_repr_style[](https://docs.python.org/3/library/sys.html#sys.float_repr_style "Permalink to this definition")

A string indicating how the [`repr()`](https://docs.python.org/3/library/functions.html#repr "repr") function behaves for floats. If the string has value `'short'` then for a finite float `x`, `repr(x)` aims to produce a short string with the property that `float(repr(x)) == x`. This is the usual behaviour in Python 3.1 and later. Otherwise, `float_repr_style` has value `'legacy'` and `repr(x)` behaves in the same way as it did in versions of Python prior to 3.1.

New in version 3.1.

sys.getallocatedblocks()[](https://docs.python.org/3/library/sys.html#sys.getallocatedblocks "Permalink to this definition")

Return the number of memory blocks currently allocated by the interpreter, regardless of their size. This function is mainly useful for tracking and debugging memory leaks. Because of the interpreter’s internal caches, the result can vary from call to call; you may have to call [`_clear_type_cache()`](https://docs.python.org/3/library/sys.html#sys._clear_type_cache "sys._clear_type_cache") and [`gc.collect()`](https://docs.python.org/3/library/gc.html#gc.collect "gc.collect") to get more predictable results.

If a Python build or implementation cannot reasonably compute this information, [`getallocatedblocks()`](https://docs.python.org/3/library/sys.html#sys.getallocatedblocks "sys.getallocatedblocks") is allowed to return 0 instead.

New in version 3.4.

sys.getunicodeinternedsize()[](https://docs.python.org/3/library/sys.html#sys.getunicodeinternedsize "Permalink to this definition")

Return the number of unicode objects that have been interned.

New in version 3.12.

sys.getandroidapilevel()[](https://docs.python.org/3/library/sys.html#sys.getandroidapilevel "Permalink to this definition")

Return the build time API version of Android as an integer.

[Availability](https://docs.python.org/3/library/intro.html#availability): Android.

New in version 3.7.

sys.getdefaultencoding()[](https://docs.python.org/3/library/sys.html#sys.getdefaultencoding "Permalink to this definition")

Return the name of the current default string encoding used by the Unicode implementation.

sys.getdlopenflags()[](https://docs.python.org/3/library/sys.html#sys.getdlopenflags "Permalink to this definition")

Return the current value of the flags that are used for `dlopen()` calls. Symbolic names for the flag values can be found in the [`os`](https://docs.python.org/3/library/os.html#module-os "os: Miscellaneous operating system interfaces.") module (`RTLD__xxx_` constants, e.g. [`os.RTLD_LAZY`](https://docs.python.org/3/library/os.html#os.RTLD_LAZY "os.RTLD_LAZY")).

[Availability](https://docs.python.org/3/library/intro.html#availability): Unix.

sys.getfilesystemencoding()[](https://docs.python.org/3/library/sys.html#sys.getfilesystemencoding "Permalink to this definition")

Get the [filesystem encoding](https://docs.python.org/3/glossary.html#term-filesystem-encoding-and-error-handler): the encoding used with the [filesystem error handler](https://docs.python.org/3/glossary.html#term-filesystem-encoding-and-error-handler) to convert between Unicode filenames and bytes filenames. The filesystem error handler is returned from [`getfilesystemencodeerrors()`](https://docs.python.org/3/library/sys.html#sys.getfilesystemencodeerrors "sys.getfilesystemencodeerrors").

For best compatibility, str should be used for filenames in all cases, although representing filenames as bytes is also supported. Functions accepting or returning filenames should support either str or bytes and internally convert to the system’s preferred representation.

[`os.fsencode()`](https://docs.python.org/3/library/os.html#os.fsencode "os.fsencode") and [`os.fsdecode()`](https://docs.python.org/3/library/os.html#os.fsdecode "os.fsdecode") should be used to ensure that the correct encoding and errors mode are used.

The [filesystem encoding and error handler](https://docs.python.org/3/glossary.html#term-filesystem-encoding-and-error-handler) are configured at Python startup by the [`PyConfig_Read()`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig_Read "PyConfig_Read") function: see [`filesystem_encoding`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig.filesystem_encoding "PyConfig.filesystem_encoding") and [`filesystem_errors`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig.filesystem_errors "PyConfig.filesystem_errors") members of [`PyConfig`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig "PyConfig").

Changed in version 3.2: [`getfilesystemencoding()`](https://docs.python.org/3/library/sys.html#sys.getfilesystemencoding "sys.getfilesystemencoding") result cannot be `None` anymore.

Changed in version 3.6: Windows is no longer guaranteed to return `'mbcs'`. See [**PEP 529**](https://peps.python.org/pep-0529/) and [`_enablelegacywindowsfsencoding()`](https://docs.python.org/3/library/sys.html#sys._enablelegacywindowsfsencoding "sys._enablelegacywindowsfsencoding") for more information.

Changed in version 3.7: Return `'utf-8'` if the [Python UTF-8 Mode](https://docs.python.org/3/library/os.html#utf8-mode) is enabled.

sys.getfilesystemencodeerrors()[](https://docs.python.org/3/library/sys.html#sys.getfilesystemencodeerrors "Permalink to this definition")

Get the [filesystem error handler](https://docs.python.org/3/glossary.html#term-filesystem-encoding-and-error-handler): the error handler used with the [filesystem encoding](https://docs.python.org/3/glossary.html#term-filesystem-encoding-and-error-handler) to convert between Unicode filenames and bytes filenames. The filesystem encoding is returned from [`getfilesystemencoding()`](https://docs.python.org/3/library/sys.html#sys.getfilesystemencoding "sys.getfilesystemencoding").

[`os.fsencode()`](https://docs.python.org/3/library/os.html#os.fsencode "os.fsencode") and [`os.fsdecode()`](https://docs.python.org/3/library/os.html#os.fsdecode "os.fsdecode") should be used to ensure that the correct encoding and errors mode are used.

The [filesystem encoding and error handler](https://docs.python.org/3/glossary.html#term-filesystem-encoding-and-error-handler) are configured at Python startup by the [`PyConfig_Read()`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig_Read "PyConfig_Read") function: see [`filesystem_encoding`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig.filesystem_encoding "PyConfig.filesystem_encoding") and [`filesystem_errors`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig.filesystem_errors "PyConfig.filesystem_errors") members of [`PyConfig`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig "PyConfig").

New in version 3.6.

sys.get_int_max_str_digits()[](https://docs.python.org/3/library/sys.html#sys.get_int_max_str_digits "Permalink to this definition")

Returns the current value for the [integer string conversion length limitation](https://docs.python.org/3/library/stdtypes.html#int-max-str-digits). See also [`set_int_max_str_digits()`](https://docs.python.org/3/library/sys.html#sys.set_int_max_str_digits "sys.set_int_max_str_digits").

New in version 3.11.

sys.getrefcount(_object_)[](https://docs.python.org/3/library/sys.html#sys.getrefcount "Permalink to this definition")

Return the reference count of the _object_. The count returned is generally one higher than you might expect, because it includes the (temporary) reference as an argument to [`getrefcount()`](https://docs.python.org/3/library/sys.html#sys.getrefcount "sys.getrefcount").

Note that the returned value may not actually reflect how many references to the object are actually held. For example, some objects are “immortal” and have a very high refcount that does not reflect the actual number of references. Consequently, do not rely on the returned value to be accurate, other than a value of 0 or 1.

Changed in version 3.12: Immortal objects have very large refcounts that do not match the actual number of references to the object.

sys.getrecursionlimit()[](https://docs.python.org/3/library/sys.html#sys.getrecursionlimit "Permalink to this definition")

Return the current value of the recursion limit, the maximum depth of the Python interpreter stack. This limit prevents infinite recursion from causing an overflow of the C stack and crashing Python. It can be set by [`setrecursionlimit()`](https://docs.python.org/3/library/sys.html#sys.setrecursionlimit "sys.setrecursionlimit").

sys.getsizeof(_object_[, _default_])[](https://docs.python.org/3/library/sys.html#sys.getsizeof "Permalink to this definition")

Return the size of an object in bytes. The object can be any type of object. All built-in objects will return correct results, but this does not have to hold true for third-party extensions as it is implementation specific.

Only the memory consumption directly attributed to the object is accounted for, not the memory consumption of objects it refers to.

If given, _default_ will be returned if the object does not provide means to retrieve the size. Otherwise a [`TypeError`](https://docs.python.org/3/library/exceptions.html#TypeError "TypeError") will be raised.

[`getsizeof()`](https://docs.python.org/3/library/sys.html#sys.getsizeof "sys.getsizeof") calls the object’s `__sizeof__` method and adds an additional garbage collector overhead if the object is managed by the garbage collector.

See [recursive sizeof recipe](https://code.activestate.com/recipes/577504/) for an example of using [`getsizeof()`](https://docs.python.org/3/library/sys.html#sys.getsizeof "sys.getsizeof") recursively to find the size of containers and all their contents.

sys.getswitchinterval()[](https://docs.python.org/3/library/sys.html#sys.getswitchinterval "Permalink to this definition")

Return the interpreter’s “thread switch interval”; see [`setswitchinterval()`](https://docs.python.org/3/library/sys.html#sys.setswitchinterval "sys.setswitchinterval").

New in version 3.2.

sys._getframe([_depth_])[](https://docs.python.org/3/library/sys.html#sys._getframe "Permalink to this definition")

Return a frame object from the call stack. If optional integer _depth_ is given, return the frame object that many calls below the top of the stack. If that is deeper than the call stack, [`ValueError`](https://docs.python.org/3/library/exceptions.html#ValueError "ValueError") is raised. The default for _depth_ is zero, returning the frame at the top of the call stack.

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `sys._getframe` with argument `frame`.

**CPython implementation detail:** This function should be used for internal and specialized purposes only. It is not guaranteed to exist in all implementations of Python.

sys._getframemodulename([_depth_])[](https://docs.python.org/3/library/sys.html#sys._getframemodulename "Permalink to this definition")

Return the name of a module from the call stack. If optional integer _depth_ is given, return the module that many calls below the top of the stack. If that is deeper than the call stack, or if the module is unidentifiable, `None` is returned. The default for _depth_ is zero, returning the module at the top of the call stack.

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `sys._getframemodulename` with argument `depth`.

**CPython implementation detail:** This function should be used for internal and specialized purposes only. It is not guaranteed to exist in all implementations of Python.

sys.getprofile()[](https://docs.python.org/3/library/sys.html#sys.getprofile "Permalink to this definition")

Get the profiler function as set by [`setprofile()`](https://docs.python.org/3/library/sys.html#sys.setprofile "sys.setprofile").

sys.gettrace()[](https://docs.python.org/3/library/sys.html#sys.gettrace "Permalink to this definition")

Get the trace function as set by [`settrace()`](https://docs.python.org/3/library/sys.html#sys.settrace "sys.settrace").

**CPython implementation detail:** The [`gettrace()`](https://docs.python.org/3/library/sys.html#sys.gettrace "sys.gettrace") function is intended only for implementing debuggers, profilers, coverage tools and the like. Its behavior is part of the implementation platform, rather than part of the language definition, and thus may not be available in all Python implementations.

sys.getwindowsversion()[](https://docs.python.org/3/library/sys.html#sys.getwindowsversion "Permalink to this definition")

Return a named tuple describing the Windows version currently running. The named elements are _major_, _minor_, _build_, _platform_, _service_pack_, _service_pack_minor_, _service_pack_major_, _suite_mask_, _product_type_ and _platform_version_. _service_pack_ contains a string, _platform_version_ a 3-tuple and all other values are integers. The components can also be accessed by name, so `sys.getwindowsversion()[0]` is equivalent to `sys.getwindowsversion().major`. For compatibility with prior versions, only the first 5 elements are retrievable by indexing.

_platform_ will be `2` (VER_PLATFORM_WIN32_NT).

_product_type_ may be one of the following values:

|Constant|Meaning|
|---|---|
|`1` (VER_NT_WORKSTATION)|The system is a workstation.|
|`2` (VER_NT_DOMAIN_CONTROLLER)|The system is a domain controller.|
|`3` (VER_NT_SERVER)|The system is a server, but not a domain controller.|

This function wraps the Win32 `GetVersionEx()` function; see the Microsoft documentation on `OSVERSIONINFOEX()` for more information about these fields.

_platform_version_ returns the major version, minor version and build number of the current operating system, rather than the version that is being emulated for the process. It is intended for use in logging rather than for feature detection.

Note

 

_platform_version_ derives the version from kernel32.dll which can be of a different version than the OS version. Please use [`platform`](https://docs.python.org/3/library/platform.html#module-platform "platform: Retrieves as much platform identifying data as possible.") module for achieving accurate OS version.

[Availability](https://docs.python.org/3/library/intro.html#availability): Windows.

Changed in version 3.2: Changed to a named tuple and added _service_pack_minor_, _service_pack_major_, _suite_mask_, and _product_type_.

Changed in version 3.6: Added _platform_version_

sys.get_asyncgen_hooks()[](https://docs.python.org/3/library/sys.html#sys.get_asyncgen_hooks "Permalink to this definition")

Returns an _asyncgen_hooks_ object, which is similar to a [`namedtuple`](https://docs.python.org/3/library/collections.html#collections.namedtuple "collections.namedtuple") of the form `(firstiter, finalizer)`, where _firstiter_ and _finalizer_ are expected to be either `None` or functions which take an [asynchronous generator iterator](https://docs.python.org/3/glossary.html#term-asynchronous-generator-iterator) as an argument, and are used to schedule finalization of an asynchronous generator by an event loop.

New in version 3.6: See [**PEP 525**](https://peps.python.org/pep-0525/) for more details.

Note

 

This function has been added on a provisional basis (see [**PEP 411**](https://peps.python.org/pep-0411/) for details.)

sys.get_coroutine_origin_tracking_depth()[](https://docs.python.org/3/library/sys.html#sys.get_coroutine_origin_tracking_depth "Permalink to this definition")

Get the current coroutine origin tracking depth, as set by [`set_coroutine_origin_tracking_depth()`](https://docs.python.org/3/library/sys.html#sys.set_coroutine_origin_tracking_depth "sys.set_coroutine_origin_tracking_depth").

New in version 3.7.

Note

 

This function has been added on a provisional basis (see [**PEP 411**](https://peps.python.org/pep-0411/) for details.) Use it only for debugging purposes.

sys.hash_info[](https://docs.python.org/3/library/sys.html#sys.hash_info "Permalink to this definition")

A [named tuple](https://docs.python.org/3/glossary.html#term-named-tuple) giving parameters of the numeric hash implementation. For more details about hashing of numeric types, see [Hashing of numeric types](https://docs.python.org/3/library/stdtypes.html#numeric-hash).

hash_info.width[](https://docs.python.org/3/library/sys.html#sys.hash_info.width "Permalink to this definition")

The width in bits used for hash values

hash_info.modulus[](https://docs.python.org/3/library/sys.html#sys.hash_info.modulus "Permalink to this definition")

The prime modulus P used for numeric hash scheme

hash_info.inf[](https://docs.python.org/3/library/sys.html#sys.hash_info.inf "Permalink to this definition")

The hash value returned for a positive infinity

hash_info.nan[](https://docs.python.org/3/library/sys.html#sys.hash_info.nan "Permalink to this definition")

(This attribute is no longer used)

hash_info.imag[](https://docs.python.org/3/library/sys.html#sys.hash_info.imag "Permalink to this definition")

The multiplier used for the imaginary part of a complex number

hash_info.algorithm[](https://docs.python.org/3/library/sys.html#sys.hash_info.algorithm "Permalink to this definition")

The name of the algorithm for hashing of str, bytes, and memoryview

hash_info.hash_bits[](https://docs.python.org/3/library/sys.html#sys.hash_info.hash_bits "Permalink to this definition")

The internal output size of the hash algorithm

hash_info.seed_bits[](https://docs.python.org/3/library/sys.html#sys.hash_info.seed_bits "Permalink to this definition")

The size of the seed key of the hash algorithm

New in version 3.2.

Changed in version 3.4: Added _algorithm_, _hash_bits_ and _seed_bits_

sys.hexversion[](https://docs.python.org/3/library/sys.html#sys.hexversion "Permalink to this definition")

The version number encoded as a single integer. This is guaranteed to increase with each version, including proper support for non-production releases. For example, to test that the Python interpreter is at least version 1.5.2, use:

if sys.hexversion >= 0x010502F0:
    # use some advanced feature
    ...
else:
    # use an alternative implementation or warn the user
    ...

This is called `hexversion` since it only really looks meaningful when viewed as the result of passing it to the built-in [`hex()`](https://docs.python.org/3/library/functions.html#hex "hex") function. The [named tuple](https://docs.python.org/3/glossary.html#term-named-tuple) [`sys.version_info`](https://docs.python.org/3/library/sys.html#sys.version_info "sys.version_info") may be used for a more human-friendly encoding of the same information.

More details of `hexversion` can be found at [API and ABI Versioning](https://docs.python.org/3/c-api/apiabiversion.html#apiabiversion).

sys.implementation[](https://docs.python.org/3/library/sys.html#sys.implementation "Permalink to this definition")

An object containing information about the implementation of the currently running Python interpreter. The following attributes are required to exist in all Python implementations.

_name_ is the implementation’s identifier, e.g. `'cpython'`. The actual string is defined by the Python implementation, but it is guaranteed to be lower case.

_version_ is a named tuple, in the same format as [`sys.version_info`](https://docs.python.org/3/library/sys.html#sys.version_info "sys.version_info"). It represents the version of the Python _implementation_. This has a distinct meaning from the specific version of the Python _language_ to which the currently running interpreter conforms, which `sys.version_info` represents. For example, for PyPy 1.8 `sys.implementation.version` might be `sys.version_info(1, 8, 0, 'final', 0)`, whereas `sys.version_info` would be `sys.version_info(2, 7, 2, 'final', 0)`. For CPython they are the same value, since it is the reference implementation.

_hexversion_ is the implementation version in hexadecimal format, like [`sys.hexversion`](https://docs.python.org/3/library/sys.html#sys.hexversion "sys.hexversion").

_cache_tag_ is the tag used by the import machinery in the filenames of cached modules. By convention, it would be a composite of the implementation’s name and version, like `'cpython-33'`. However, a Python implementation may use some other value if appropriate. If `cache_tag` is set to `None`, it indicates that module caching should be disabled.

[`sys.implementation`](https://docs.python.org/3/library/sys.html#sys.implementation "sys.implementation") may contain additional attributes specific to the Python implementation. These non-standard attributes must start with an underscore, and are not described here. Regardless of its contents, [`sys.implementation`](https://docs.python.org/3/library/sys.html#sys.implementation "sys.implementation") will not change during a run of the interpreter, nor between implementation versions. (It may change between Python language versions, however.) See [**PEP 421**](https://peps.python.org/pep-0421/) for more information.

New in version 3.3.

Note

 

The addition of new required attributes must go through the normal PEP process. See [**PEP 421**](https://peps.python.org/pep-0421/) for more information.

sys.int_info[](https://docs.python.org/3/library/sys.html#sys.int_info "Permalink to this definition")

A [named tuple](https://docs.python.org/3/glossary.html#term-named-tuple) that holds information about Python’s internal representation of integers. The attributes are read only.

int_info.bits_per_digit[](https://docs.python.org/3/library/sys.html#sys.int_info.bits_per_digit "Permalink to this definition")

The number of bits held in each digit. Python integers are stored internally in base `2**int_info.bits_per_digit`.

int_info.sizeof_digit[](https://docs.python.org/3/library/sys.html#sys.int_info.sizeof_digit "Permalink to this definition")

The size in bytes of the C type used to represent a digit.

int_info.default_max_str_digits[](https://docs.python.org/3/library/sys.html#sys.int_info.default_max_str_digits "Permalink to this definition")

The default value for [`sys.get_int_max_str_digits()`](https://docs.python.org/3/library/sys.html#sys.get_int_max_str_digits "sys.get_int_max_str_digits") when it is not otherwise explicitly configured.

int_info.str_digits_check_threshold[](https://docs.python.org/3/library/sys.html#sys.int_info.str_digits_check_threshold "Permalink to this definition")

The minimum non-zero value for [`sys.set_int_max_str_digits()`](https://docs.python.org/3/library/sys.html#sys.set_int_max_str_digits "sys.set_int_max_str_digits"), [`PYTHONINTMAXSTRDIGITS`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONINTMAXSTRDIGITS), or [`-X int_max_str_digits`](https://docs.python.org/3/using/cmdline.html#cmdoption-X).

New in version 3.1.

Changed in version 3.11: Added [`default_max_str_digits`](https://docs.python.org/3/library/sys.html#sys.int_info.default_max_str_digits "sys.int_info.default_max_str_digits") and [`str_digits_check_threshold`](https://docs.python.org/3/library/sys.html#sys.int_info.str_digits_check_threshold "sys.int_info.str_digits_check_threshold").

sys.__interactivehook__[](https://docs.python.org/3/library/sys.html#sys.__interactivehook__ "Permalink to this definition")

When this attribute exists, its value is automatically called (with no arguments) when the interpreter is launched in [interactive mode](https://docs.python.org/3/tutorial/interpreter.html#tut-interactive). This is done after the [`PYTHONSTARTUP`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONSTARTUP) file is read, so that you can set this hook there. The [`site`](https://docs.python.org/3/library/site.html#module-site "site: Module responsible for site-specific configuration.") module [sets this](https://docs.python.org/3/library/site.html#rlcompleter-config).

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `cpython.run_interactivehook` with the hook object as the argument when the hook is called on startup.

New in version 3.4.

sys.intern(_string_)[](https://docs.python.org/3/library/sys.html#sys.intern "Permalink to this definition")

Enter _string_ in the table of “interned” strings and return the interned string – which is _string_ itself or a copy. Interning strings is useful to gain a little performance on dictionary lookup – if the keys in a dictionary are interned, and the lookup key is interned, the key comparisons (after hashing) can be done by a pointer compare instead of a string compare. Normally, the names used in Python programs are automatically interned, and the dictionaries used to hold module, class or instance attributes have interned keys.

Interned strings are not immortal; you must keep a reference to the return value of [`intern()`](https://docs.python.org/3/library/sys.html#sys.intern "sys.intern") around to benefit from it.

sys.is_finalizing()[](https://docs.python.org/3/library/sys.html#sys.is_finalizing "Permalink to this definition")

Return [`True`](https://docs.python.org/3/library/constants.html#True "True") if the Python interpreter is [shutting down](https://docs.python.org/3/glossary.html#term-interpreter-shutdown), [`False`](https://docs.python.org/3/library/constants.html#False "False") otherwise.

New in version 3.5.

sys.last_exc[](https://docs.python.org/3/library/sys.html#sys.last_exc "Permalink to this definition")

This variable is not always defined; it is set to the exception instance when an exception is not handled and the interpreter prints an error message and a stack traceback. Its intended use is to allow an interactive user to import a debugger module and engage in post-mortem debugging without having to re-execute the command that caused the error. (Typical use is `import pdb; pdb.pm()` to enter the post-mortem debugger; see [`pdb`](https://docs.python.org/3/library/pdb.html#module-pdb "pdb: The Python debugger for interactive interpreters.") module for more information.)

New in version 3.12.

sys.last_type[](https://docs.python.org/3/library/sys.html#sys.last_type "Permalink to this definition")

sys.last_value[](https://docs.python.org/3/library/sys.html#sys.last_value "Permalink to this definition")

sys.last_traceback[](https://docs.python.org/3/library/sys.html#sys.last_traceback "Permalink to this definition")

These three variables are deprecated; use [`sys.last_exc`](https://docs.python.org/3/library/sys.html#sys.last_exc "sys.last_exc") instead. They hold the legacy representation of `sys.last_exc`, as returned from [`exc_info()`](https://docs.python.org/3/library/sys.html#sys.exc_info "sys.exc_info") above.

sys.maxsize[](https://docs.python.org/3/library/sys.html#sys.maxsize "Permalink to this definition")

An integer giving the maximum value a variable of type [`Py_ssize_t`](https://docs.python.org/3/c-api/intro.html#c.Py_ssize_t "Py_ssize_t") can take. It’s usually `2**31 - 1` on a 32-bit platform and `2**63 - 1` on a 64-bit platform.

sys.maxunicode[](https://docs.python.org/3/library/sys.html#sys.maxunicode "Permalink to this definition")

An integer giving the value of the largest Unicode code point, i.e. `1114111` (`0x10FFFF` in hexadecimal).

Changed in version 3.3: Before [**PEP 393**](https://peps.python.org/pep-0393/), `sys.maxunicode` used to be either `0xFFFF` or `0x10FFFF`, depending on the configuration option that specified whether Unicode characters were stored as UCS-2 or UCS-4.

sys.meta_path[](https://docs.python.org/3/library/sys.html#sys.meta_path "Permalink to this definition")

A list of [meta path finder](https://docs.python.org/3/glossary.html#term-meta-path-finder) objects that have their [`find_spec()`](https://docs.python.org/3/library/importlib.html#importlib.abc.MetaPathFinder.find_spec "importlib.abc.MetaPathFinder.find_spec") methods called to see if one of the objects can find the module to be imported. By default, it holds entries that implement Python’s default import semantics. The [`find_spec()`](https://docs.python.org/3/library/importlib.html#importlib.abc.MetaPathFinder.find_spec "importlib.abc.MetaPathFinder.find_spec") method is called with at least the absolute name of the module being imported. If the module to be imported is contained in a package, then the parent package’s [`__path__`](https://docs.python.org/3/reference/import.html#path__ "__path__") attribute is passed in as a second argument. The method returns a [module spec](https://docs.python.org/3/glossary.html#term-module-spec), or `None` if the module cannot be found.

See also

[`importlib.abc.MetaPathFinder`](https://docs.python.org/3/library/importlib.html#importlib.abc.MetaPathFinder "importlib.abc.MetaPathFinder")

The abstract base class defining the interface of finder objects on [`meta_path`](https://docs.python.org/3/library/sys.html#sys.meta_path "sys.meta_path").

[`importlib.machinery.ModuleSpec`](https://docs.python.org/3/library/importlib.html#importlib.machinery.ModuleSpec "importlib.machinery.ModuleSpec")

The concrete class which [`find_spec()`](https://docs.python.org/3/library/importlib.html#importlib.abc.MetaPathFinder.find_spec "importlib.abc.MetaPathFinder.find_spec") should return instances of.

Changed in version 3.4: [Module specs](https://docs.python.org/3/glossary.html#term-module-spec) were introduced in Python 3.4, by [**PEP 451**](https://peps.python.org/pep-0451/). Earlier versions of Python looked for a method called `find_module()`. This is still called as a fallback if a [`meta_path`](https://docs.python.org/3/library/sys.html#sys.meta_path "sys.meta_path") entry doesn’t have a [`find_spec()`](https://docs.python.org/3/library/importlib.html#importlib.abc.MetaPathFinder.find_spec "importlib.abc.MetaPathFinder.find_spec") method.

sys.modules[](https://docs.python.org/3/library/sys.html#sys.modules "Permalink to this definition")

This is a dictionary that maps module names to modules which have already been loaded. This can be manipulated to force reloading of modules and other tricks. However, replacing the dictionary will not necessarily work as expected and deleting essential items from the dictionary may cause Python to fail. If you want to iterate over this global dictionary always use `sys.modules.copy()` or `tuple(sys.modules)` to avoid exceptions as its size may change during iteration as a side effect of code or activity in other threads.

sys.orig_argv[](https://docs.python.org/3/library/sys.html#sys.orig_argv "Permalink to this definition")

The list of the original command line arguments passed to the Python executable.

See also [`sys.argv`](https://docs.python.org/3/library/sys.html#sys.argv "sys.argv").

New in version 3.10.

sys.path[](https://docs.python.org/3/library/sys.html#sys.path "Permalink to this definition")

A list of strings that specifies the search path for modules. Initialized from the environment variable [`PYTHONPATH`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPATH), plus an installation-dependent default.

By default, as initialized upon program startup, a potentially unsafe path is prepended to [`sys.path`](https://docs.python.org/3/library/sys.html#sys.path "sys.path") (_before_ the entries inserted as a result of [`PYTHONPATH`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPATH)):

- `python -m module` command line: prepend the current working directory.
    
- `python script.py` command line: prepend the script’s directory. If it’s a symbolic link, resolve symbolic links.
    
- `python -c code` and `python` (REPL) command lines: prepend an empty string, which means the current working directory.
    

To not prepend this potentially unsafe path, use the [`-P`](https://docs.python.org/3/using/cmdline.html#cmdoption-P) command line option or the [`PYTHONSAFEPATH`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONSAFEPATH) environment variable.

A program is free to modify this list for its own purposes. Only strings should be added to [`sys.path`](https://docs.python.org/3/library/sys.html#sys.path "sys.path"); all other data types are ignored during import.

See also

- Module [`site`](https://docs.python.org/3/library/site.html#module-site "site: Module responsible for site-specific configuration.") This describes how to use .pth files to extend [`sys.path`](https://docs.python.org/3/library/sys.html#sys.path "sys.path").
    

sys.path_hooks[](https://docs.python.org/3/library/sys.html#sys.path_hooks "Permalink to this definition")

A list of callables that take a path argument to try to create a [finder](https://docs.python.org/3/glossary.html#term-finder) for the path. If a finder can be created, it is to be returned by the callable, else raise [`ImportError`](https://docs.python.org/3/library/exceptions.html#ImportError "ImportError").

Originally specified in [**PEP 302**](https://peps.python.org/pep-0302/).

sys.path_importer_cache[](https://docs.python.org/3/library/sys.html#sys.path_importer_cache "Permalink to this definition")

A dictionary acting as a cache for [finder](https://docs.python.org/3/glossary.html#term-finder) objects. The keys are paths that have been passed to [`sys.path_hooks`](https://docs.python.org/3/library/sys.html#sys.path_hooks "sys.path_hooks") and the values are the finders that are found. If a path is a valid file system path but no finder is found on [`sys.path_hooks`](https://docs.python.org/3/library/sys.html#sys.path_hooks "sys.path_hooks") then `None` is stored.

Originally specified in [**PEP 302**](https://peps.python.org/pep-0302/).

sys.platform[](https://docs.python.org/3/library/sys.html#sys.platform "Permalink to this definition")

This string contains a platform identifier that can be used to append platform-specific components to [`sys.path`](https://docs.python.org/3/library/sys.html#sys.path "sys.path"), for instance.

For Unix systems, except on Linux and AIX, this is the lowercased OS name as returned by `uname -s` with the first part of the version as returned by `uname -r` appended, e.g. `'sunos5'` or `'freebsd8'`, _at the time when Python was built_. Unless you want to test for a specific system version, it is therefore recommended to use the following idiom:

if sys.platform.startswith('freebsd'):
    # FreeBSD-specific code here...
elif sys.platform.startswith('linux'):
    # Linux-specific code here...
elif sys.platform.startswith('aix'):
    # AIX-specific code here...

For other systems, the values are:

|System|`platform` value|
|---|---|
|AIX|`'aix'`|
|Emscripten|`'emscripten'`|
|Linux|`'linux'`|
|WASI|`'wasi'`|
|Windows|`'win32'`|
|Windows/Cygwin|`'cygwin'`|
|macOS|`'darwin'`|

Changed in version 3.3: On Linux, [`sys.platform`](https://docs.python.org/3/library/sys.html#sys.platform "sys.platform") doesn’t contain the major version anymore. It is always `'linux'`, instead of `'linux2'` or `'linux3'`. Since older Python versions include the version number, it is recommended to always use the `startswith` idiom presented above.

Changed in version 3.8: On AIX, [`sys.platform`](https://docs.python.org/3/library/sys.html#sys.platform "sys.platform") doesn’t contain the major version anymore. It is always `'aix'`, instead of `'aix5'` or `'aix7'`. Since older Python versions include the version number, it is recommended to always use the `startswith` idiom presented above.

See also

 

[`os.name`](https://docs.python.org/3/library/os.html#os.name "os.name") has a coarser granularity. [`os.uname()`](https://docs.python.org/3/library/os.html#os.uname "os.uname") gives system-dependent version information.

The [`platform`](https://docs.python.org/3/library/platform.html#module-platform "platform: Retrieves as much platform identifying data as possible.") module provides detailed checks for the system’s identity.

sys.platlibdir[](https://docs.python.org/3/library/sys.html#sys.platlibdir "Permalink to this definition")

Name of the platform-specific library directory. It is used to build the path of standard library and the paths of installed extension modules.

It is equal to `"lib"` on most platforms. On Fedora and SuSE, it is equal to `"lib64"` on 64-bit platforms which gives the following `sys.path` paths (where `X.Y` is the Python `major.minor` version):

- `/usr/lib64/pythonX.Y/`: Standard library (like `os.py` of the [`os`](https://docs.python.org/3/library/os.html#module-os "os: Miscellaneous operating system interfaces.") module)
    
- `/usr/lib64/pythonX.Y/lib-dynload/`: C extension modules of the standard library (like the [`errno`](https://docs.python.org/3/library/errno.html#module-errno "errno: Standard errno system symbols.") module, the exact filename is platform specific)
    
- `/usr/lib/pythonX.Y/site-packages/` (always use `lib`, not [`sys.platlibdir`](https://docs.python.org/3/library/sys.html#sys.platlibdir "sys.platlibdir")): Third-party modules
    
- `/usr/lib64/pythonX.Y/site-packages/`: C extension modules of third-party packages
    

New in version 3.9.

sys.prefix[](https://docs.python.org/3/library/sys.html#sys.prefix "Permalink to this definition")

A string giving the site-specific directory prefix where the platform independent Python files are installed; on Unix, the default is `/usr/local`. This can be set at build time with the [`--prefix`](https://docs.python.org/3/using/configure.html#cmdoption-prefix) argument to the **configure** script. See [Installation paths](https://docs.python.org/3/library/sysconfig.html#installation-paths) for derived paths.

Note

 

If a [virtual environment](https://docs.python.org/3/library/venv.html#venv-def) is in effect, this value will be changed in `site.py` to point to the virtual environment. The value for the Python installation will still be available, via [`base_prefix`](https://docs.python.org/3/library/sys.html#sys.base_prefix "sys.base_prefix").

sys.ps1[](https://docs.python.org/3/library/sys.html#sys.ps1 "Permalink to this definition")

sys.ps2[](https://docs.python.org/3/library/sys.html#sys.ps2 "Permalink to this definition")

Strings specifying the primary and secondary prompt of the interpreter. These are only defined if the interpreter is in interactive mode. Their initial values in this case are `'>>> '` and `'... '`. If a non-string object is assigned to either variable, its [`str()`](https://docs.python.org/3/library/stdtypes.html#str "str") is re-evaluated each time the interpreter prepares to read a new interactive command; this can be used to implement a dynamic prompt.

sys.setdlopenflags(_n_)[](https://docs.python.org/3/library/sys.html#sys.setdlopenflags "Permalink to this definition")

Set the flags used by the interpreter for `dlopen()` calls, such as when the interpreter loads extension modules. Among other things, this will enable a lazy resolving of symbols when importing a module, if called as `sys.setdlopenflags(0)`. To share symbols across extension modules, call as `sys.setdlopenflags(os.RTLD_GLOBAL)`. Symbolic names for the flag values can be found in the [`os`](https://docs.python.org/3/library/os.html#module-os "os: Miscellaneous operating system interfaces.") module (`RTLD__xxx_` constants, e.g. [`os.RTLD_LAZY`](https://docs.python.org/3/library/os.html#os.RTLD_LAZY "os.RTLD_LAZY")).

[Availability](https://docs.python.org/3/library/intro.html#availability): Unix.

sys.set_int_max_str_digits(_maxdigits_)[](https://docs.python.org/3/library/sys.html#sys.set_int_max_str_digits "Permalink to this definition")

Set the [integer string conversion length limitation](https://docs.python.org/3/library/stdtypes.html#int-max-str-digits) used by this interpreter. See also [`get_int_max_str_digits()`](https://docs.python.org/3/library/sys.html#sys.get_int_max_str_digits "sys.get_int_max_str_digits").

New in version 3.11.

sys.setprofile(_profilefunc_)[](https://docs.python.org/3/library/sys.html#sys.setprofile "Permalink to this definition")

Set the system’s profile function, which allows you to implement a Python source code profiler in Python. See chapter [The Python Profilers](https://docs.python.org/3/library/profile.html#profile) for more information on the Python profiler. The system’s profile function is called similarly to the system’s trace function (see [`settrace()`](https://docs.python.org/3/library/sys.html#sys.settrace "sys.settrace")), but it is called with different events, for example it isn’t called for each executed line of code (only on call and return, but the return event is reported even when an exception has been set). The function is thread-specific, but there is no way for the profiler to know about context switches between threads, so it does not make sense to use this in the presence of multiple threads. Also, its return value is not used, so it can simply return `None`. Error in the profile function will cause itself unset.

Note

 

The same tracing mechanism is used for `setprofile()` as [`settrace()`](https://docs.python.org/3/library/sys.html#sys.settrace "sys.settrace"). To trace calls with `setprofile()` inside a tracing function (e.g. in a debugger breakpoint), see [`call_tracing()`](https://docs.python.org/3/library/sys.html#sys.call_tracing "sys.call_tracing").

Profile functions should have three arguments: _frame_, _event_, and _arg_. _frame_ is the current stack frame. _event_ is a string: `'call'`, `'return'`, `'c_call'`, `'c_return'`, or `'c_exception'`. _arg_ depends on the event type.

The events have the following meaning:

`'call'`

A function is called (or some other code block entered). The profile function is called; _arg_ is `None`.

`'return'`

A function (or other code block) is about to return. The profile function is called; _arg_ is the value that will be returned, or `None` if the event is caused by an exception being raised.

`'c_call'`

A C function is about to be called. This may be an extension function or a built-in. _arg_ is the C function object.

`'c_return'`

A C function has returned. _arg_ is the C function object.

`'c_exception'`

A C function has raised an exception. _arg_ is the C function object.

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `sys.setprofile` with no arguments.

sys.setrecursionlimit(_limit_)[](https://docs.python.org/3/library/sys.html#sys.setrecursionlimit "Permalink to this definition")

Set the maximum depth of the Python interpreter stack to _limit_. This limit prevents infinite recursion from causing an overflow of the C stack and crashing Python.

The highest possible limit is platform-dependent. A user may need to set the limit higher when they have a program that requires deep recursion and a platform that supports a higher limit. This should be done with care, because a too-high limit can lead to a crash.

If the new limit is too low at the current recursion depth, a [`RecursionError`](https://docs.python.org/3/library/exceptions.html#RecursionError "RecursionError") exception is raised.

Changed in version 3.5.1: A [`RecursionError`](https://docs.python.org/3/library/exceptions.html#RecursionError "RecursionError") exception is now raised if the new limit is too low at the current recursion depth.

sys.setswitchinterval(_interval_)[](https://docs.python.org/3/library/sys.html#sys.setswitchinterval "Permalink to this definition")

Set the interpreter’s thread switch interval (in seconds). This floating-point value determines the ideal duration of the “timeslices” allocated to concurrently running Python threads. Please note that the actual value can be higher, especially if long-running internal functions or methods are used. Also, which thread becomes scheduled at the end of the interval is the operating system’s decision. The interpreter doesn’t have its own scheduler.

New in version 3.2.

sys.settrace(_tracefunc_)[](https://docs.python.org/3/library/sys.html#sys.settrace "Permalink to this definition")

Set the system’s trace function, which allows you to implement a Python source code debugger in Python. The function is thread-specific; for a debugger to support multiple threads, it must register a trace function using [`settrace()`](https://docs.python.org/3/library/sys.html#sys.settrace "sys.settrace") for each thread being debugged or use [`threading.settrace()`](https://docs.python.org/3/library/threading.html#threading.settrace "threading.settrace").

Trace functions should have three arguments: _frame_, _event_, and _arg_. _frame_ is the current stack frame. _event_ is a string: `'call'`, `'line'`, `'return'`, `'exception'` or `'opcode'`. _arg_ depends on the event type.

The trace function is invoked (with _event_ set to `'call'`) whenever a new local scope is entered; it should return a reference to a local trace function to be used for the new scope, or `None` if the scope shouldn’t be traced.

The local trace function should return a reference to itself, or to another function which would then be used as the local trace function for the scope.

If there is any error occurred in the trace function, it will be unset, just like `settrace(None)` is called.

Note

 

Tracing is disabled while calling the trace function (e.g. a function set by `settrace()`). For recursive tracing see [`call_tracing()`](https://docs.python.org/3/library/sys.html#sys.call_tracing "sys.call_tracing").

The events have the following meaning:

`'call'`

A function is called (or some other code block entered). The global trace function is called; _arg_ is `None`; the return value specifies the local trace function.

`'line'`

The interpreter is about to execute a new line of code or re-execute the condition of a loop. The local trace function is called; _arg_ is `None`; the return value specifies the new local trace function. See `Objects/lnotab_notes.txt` for a detailed explanation of how this works. Per-line events may be disabled for a frame by setting [`f_trace_lines`](https://docs.python.org/3/reference/datamodel.html#frame.f_trace_lines "frame.f_trace_lines") to [`False`](https://docs.python.org/3/library/constants.html#False "False") on that [frame](https://docs.python.org/3/reference/datamodel.html#frame-objects).

`'return'`

A function (or other code block) is about to return. The local trace function is called; _arg_ is the value that will be returned, or `None` if the event is caused by an exception being raised. The trace function’s return value is ignored.

`'exception'`

An exception has occurred. The local trace function is called; _arg_ is a tuple `(exception, value, traceback)`; the return value specifies the new local trace function.

`'opcode'`

The interpreter is about to execute a new opcode (see [`dis`](https://docs.python.org/3/library/dis.html#module-dis "dis: Disassembler for Python bytecode.") for opcode details). The local trace function is called; _arg_ is `None`; the return value specifies the new local trace function. Per-opcode events are not emitted by default: they must be explicitly requested by setting [`f_trace_opcodes`](https://docs.python.org/3/reference/datamodel.html#frame.f_trace_opcodes "frame.f_trace_opcodes") to [`True`](https://docs.python.org/3/library/constants.html#True "True") on the [frame](https://docs.python.org/3/reference/datamodel.html#frame-objects).

Note that as an exception is propagated down the chain of callers, an `'exception'` event is generated at each level.

For more fine-grained usage, it’s possible to set a trace function by assigning `frame.f_trace = tracefunc` explicitly, rather than relying on it being set indirectly via the return value from an already installed trace function. This is also required for activating the trace function on the current frame, which [`settrace()`](https://docs.python.org/3/library/sys.html#sys.settrace "sys.settrace") doesn’t do. Note that in order for this to work, a global tracing function must have been installed with [`settrace()`](https://docs.python.org/3/library/sys.html#sys.settrace "sys.settrace") in order to enable the runtime tracing machinery, but it doesn’t need to be the same tracing function (e.g. it could be a low overhead tracing function that simply returns `None` to disable itself immediately on each frame).

For more information on code and frame objects, refer to [The standard type hierarchy](https://docs.python.org/3/reference/datamodel.html#types).

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `sys.settrace` with no arguments.

**CPython implementation detail:** The [`settrace()`](https://docs.python.org/3/library/sys.html#sys.settrace "sys.settrace") function is intended only for implementing debuggers, profilers, coverage tools and the like. Its behavior is part of the implementation platform, rather than part of the language definition, and thus may not be available in all Python implementations.

Changed in version 3.7: `'opcode'` event type added; [`f_trace_lines`](https://docs.python.org/3/reference/datamodel.html#frame.f_trace_lines "frame.f_trace_lines") and [`f_trace_opcodes`](https://docs.python.org/3/reference/datamodel.html#frame.f_trace_opcodes "frame.f_trace_opcodes") attributes added to frames

sys.set_asyncgen_hooks(_firstiter_, _finalizer_)[](https://docs.python.org/3/library/sys.html#sys.set_asyncgen_hooks "Permalink to this definition")

Accepts two optional keyword arguments which are callables that accept an [asynchronous generator iterator](https://docs.python.org/3/glossary.html#term-asynchronous-generator-iterator) as an argument. The _firstiter_ callable will be called when an asynchronous generator is iterated for the first time. The _finalizer_ will be called when an asynchronous generator is about to be garbage collected.

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `sys.set_asyncgen_hooks_firstiter` with no arguments.

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `sys.set_asyncgen_hooks_finalizer` with no arguments.

Two auditing events are raised because the underlying API consists of two calls, each of which must raise its own event.

New in version 3.6: See [**PEP 525**](https://peps.python.org/pep-0525/) for more details, and for a reference example of a _finalizer_ method see the implementation of `asyncio.Loop.shutdown_asyncgens` in [Lib/asyncio/base_events.py](https://github.com/python/cpython/tree/3.12/Lib/asyncio/base_events.py)

Note

 

This function has been added on a provisional basis (see [**PEP 411**](https://peps.python.org/pep-0411/) for details.)

sys.set_coroutine_origin_tracking_depth(_depth_)[](https://docs.python.org/3/library/sys.html#sys.set_coroutine_origin_tracking_depth "Permalink to this definition")

Allows enabling or disabling coroutine origin tracking. When enabled, the `cr_origin` attribute on coroutine objects will contain a tuple of (filename, line number, function name) tuples describing the traceback where the coroutine object was created, with the most recent call first. When disabled, `cr_origin` will be None.

To enable, pass a _depth_ value greater than zero; this sets the number of frames whose information will be captured. To disable, pass set _depth_ to zero.

This setting is thread-specific.

New in version 3.7.

Note

 

This function has been added on a provisional basis (see [**PEP 411**](https://peps.python.org/pep-0411/) for details.) Use it only for debugging purposes.

sys.activate_stack_trampoline(_backend_, _/_)[](https://docs.python.org/3/library/sys.html#sys.activate_stack_trampoline "Permalink to this definition")

Activate the stack profiler trampoline _backend_. The only supported backend is `"perf"`.

[Availability](https://docs.python.org/3/library/intro.html#availability): Linux.

New in version 3.12.

See also

- [Python support for the Linux perf profiler](https://docs.python.org/3/howto/perf_profiling.html#perf-profiling)
    
- [https://perf.wiki.kernel.org](https://perf.wiki.kernel.org/)
    

sys.deactivate_stack_trampoline()[](https://docs.python.org/3/library/sys.html#sys.deactivate_stack_trampoline "Permalink to this definition")

Deactivate the current stack profiler trampoline backend.

If no stack profiler is activated, this function has no effect.

[Availability](https://docs.python.org/3/library/intro.html#availability): Linux.

New in version 3.12.

sys.is_stack_trampoline_active()[](https://docs.python.org/3/library/sys.html#sys.is_stack_trampoline_active "Permalink to this definition")

Return `True` if a stack profiler trampoline is active.

[Availability](https://docs.python.org/3/library/intro.html#availability): Linux.

New in version 3.12.

sys._enablelegacywindowsfsencoding()[](https://docs.python.org/3/library/sys.html#sys._enablelegacywindowsfsencoding "Permalink to this definition")

Changes the [filesystem encoding and error handler](https://docs.python.org/3/glossary.html#term-filesystem-encoding-and-error-handler) to ‘mbcs’ and ‘replace’ respectively, for consistency with versions of Python prior to 3.6.

This is equivalent to defining the [`PYTHONLEGACYWINDOWSFSENCODING`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSFSENCODING) environment variable before launching Python.

See also [`sys.getfilesystemencoding()`](https://docs.python.org/3/library/sys.html#sys.getfilesystemencoding "sys.getfilesystemencoding") and [`sys.getfilesystemencodeerrors()`](https://docs.python.org/3/library/sys.html#sys.getfilesystemencodeerrors "sys.getfilesystemencodeerrors").

[Availability](https://docs.python.org/3/library/intro.html#availability): Windows.

New in version 3.6: See [**PEP 529**](https://peps.python.org/pep-0529/) for more details.

sys.stdin[](https://docs.python.org/3/library/sys.html#sys.stdin "Permalink to this definition")

sys.stdout[](https://docs.python.org/3/library/sys.html#sys.stdout "Permalink to this definition")

sys.stderr[](https://docs.python.org/3/library/sys.html#sys.stderr "Permalink to this definition")

[File objects](https://docs.python.org/3/glossary.html#term-file-object) used by the interpreter for standard input, output and errors:

- `stdin` is used for all interactive input (including calls to [`input()`](https://docs.python.org/3/library/functions.html#input "input"));
    
- `stdout` is used for the output of [`print()`](https://docs.python.org/3/library/functions.html#print "print") and [expression](https://docs.python.org/3/glossary.html#term-expression) statements and for the prompts of [`input()`](https://docs.python.org/3/library/functions.html#input "input");
    
- The interpreter’s own prompts and its error messages go to `stderr`.
    

These streams are regular [text files](https://docs.python.org/3/glossary.html#term-text-file) like those returned by the [`open()`](https://docs.python.org/3/library/functions.html#open "open") function. Their parameters are chosen as follows:

- The encoding and error handling are is initialized from [`PyConfig.stdio_encoding`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig.stdio_encoding "PyConfig.stdio_encoding") and [`PyConfig.stdio_errors`](https://docs.python.org/3/c-api/init_config.html#c.PyConfig.stdio_errors "PyConfig.stdio_errors").
    
    On Windows, UTF-8 is used for the console device. Non-character devices such as disk files and pipes use the system locale encoding (i.e. the ANSI codepage). Non-console character devices such as NUL (i.e. where `isatty()` returns `True`) use the value of the console input and output codepages at startup, respectively for stdin and stdout/stderr. This defaults to the system [locale encoding](https://docs.python.org/3/glossary.html#term-locale-encoding) if the process is not initially attached to a console.
    
    The special behaviour of the console can be overridden by setting the environment variable PYTHONLEGACYWINDOWSSTDIO before starting Python. In that case, the console codepages are used as for any other character device.
    
    Under all platforms, you can override the character encoding by setting the [`PYTHONIOENCODING`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONIOENCODING) environment variable before starting Python or by using the new [`-X`](https://docs.python.org/3/using/cmdline.html#cmdoption-X) `utf8` command line option and [`PYTHONUTF8`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONUTF8) environment variable. However, for the Windows console, this only applies when [`PYTHONLEGACYWINDOWSSTDIO`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSSTDIO) is also set.
    
- When interactive, the `stdout` stream is line-buffered. Otherwise, it is block-buffered like regular text files. The `stderr` stream is line-buffered in both cases. You can make both streams unbuffered by passing the [`-u`](https://docs.python.org/3/using/cmdline.html#cmdoption-u) command-line option or setting the [`PYTHONUNBUFFERED`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONUNBUFFERED) environment variable.
    

Changed in version 3.9: Non-interactive `stderr` is now line-buffered instead of fully buffered.

Note

 

To write or read binary data from/to the standard streams, use the underlying binary [`buffer`](https://docs.python.org/3/library/io.html#io.TextIOBase.buffer "io.TextIOBase.buffer") object. For example, to write bytes to [`stdout`](https://docs.python.org/3/library/sys.html#sys.stdout "sys.stdout"), use `sys.stdout.buffer.write(b'abc')`.

However, if you are writing a library (and do not control in which context its code will be executed), be aware that the standard streams may be replaced with file-like objects like [`io.StringIO`](https://docs.python.org/3/library/io.html#io.StringIO "io.StringIO") which do not support the `buffer` attribute.

sys.__stdin__[](https://docs.python.org/3/library/sys.html#sys.__stdin__ "Permalink to this definition")

sys.__stdout__[](https://docs.python.org/3/library/sys.html#sys.__stdout__ "Permalink to this definition")

sys.__stderr__[](https://docs.python.org/3/library/sys.html#sys.__stderr__ "Permalink to this definition")

These objects contain the original values of `stdin`, `stderr` and `stdout` at the start of the program. They are used during finalization, and could be useful to print to the actual standard stream no matter if the `sys.std*` object has been redirected.

It can also be used to restore the actual files to known working file objects in case they have been overwritten with a broken object. However, the preferred way to do this is to explicitly save the previous stream before replacing it, and restore the saved object.

Note

 

Under some conditions `stdin`, `stdout` and `stderr` as well as the original values `__stdin__`, `__stdout__` and `__stderr__` can be `None`. It is usually the case for Windows GUI apps that aren’t connected to a console and Python apps started with **pythonw**.

sys.stdlib_module_names[](https://docs.python.org/3/library/sys.html#sys.stdlib_module_names "Permalink to this definition")

A frozenset of strings containing the names of standard library modules.

It is the same on all platforms. Modules which are not available on some platforms and modules disabled at Python build are also listed. All module kinds are listed: pure Python, built-in, frozen and extension modules. Test modules are excluded.

For packages, only the main package is listed: sub-packages and sub-modules are not listed. For example, the `email` package is listed, but the `email.mime` sub-package and the `email.message` sub-module are not listed.

See also the [`sys.builtin_module_names`](https://docs.python.org/3/library/sys.html#sys.builtin_module_names "sys.builtin_module_names") list.

New in version 3.10.

sys.thread_info[](https://docs.python.org/3/library/sys.html#sys.thread_info "Permalink to this definition")

A [named tuple](https://docs.python.org/3/glossary.html#term-named-tuple) holding information about the thread implementation.

thread_info.name[](https://docs.python.org/3/library/sys.html#sys.thread_info.name "Permalink to this definition")

The name of the thread implementation:

- `"nt"`: Windows threads
    
- `"pthread"`: POSIX threads
    
- `"pthread-stubs"`: stub POSIX threads (on WebAssembly platforms without threading support)
    
- `"solaris"`: Solaris threads
    

thread_info.lock[](https://docs.python.org/3/library/sys.html#sys.thread_info.lock "Permalink to this definition")

The name of the lock implementation:

- `"semaphore"`: a lock uses a semaphore
    
- `"mutex+cond"`: a lock uses a mutex and a condition variable
    
- `None` if this information is unknown
    

thread_info.version[](https://docs.python.org/3/library/sys.html#sys.thread_info.version "Permalink to this definition")

The name and version of the thread library. It is a string, or `None` if this information is unknown.

New in version 3.3.

sys.tracebacklimit[](https://docs.python.org/3/library/sys.html#sys.tracebacklimit "Permalink to this definition")

When this variable is set to an integer value, it determines the maximum number of levels of traceback information printed when an unhandled exception occurs. The default is `1000`. When set to `0` or less, all traceback information is suppressed and only the exception type and value are printed.

sys.unraisablehook(_unraisable_, _/_)[](https://docs.python.org/3/library/sys.html#sys.unraisablehook "Permalink to this definition")

Handle an unraisable exception.

Called when an exception has occurred but there is no way for Python to handle it. For example, when a destructor raises an exception or during garbage collection ([`gc.collect()`](https://docs.python.org/3/library/gc.html#gc.collect "gc.collect")).

The _unraisable_ argument has the following attributes:

- `exc_type`: Exception type.
    
- `exc_value`: Exception value, can be `None`.
    
- `exc_traceback`: Exception traceback, can be `None`.
    
- `err_msg`: Error message, can be `None`.
    
- `object`: Object causing the exception, can be `None`.
    

The default hook formats `err_msg` and `object` as: `f'{err_msg}: {object!r}'`; use “Exception ignored in” error message if `err_msg` is `None`.

[`sys.unraisablehook()`](https://docs.python.org/3/library/sys.html#sys.unraisablehook "sys.unraisablehook") can be overridden to control how unraisable exceptions are handled.

See also

 

[`excepthook()`](https://docs.python.org/3/library/sys.html#sys.excepthook "sys.excepthook") which handles uncaught exceptions.

Warning

 

Storing `exc_value` using a custom hook can create a reference cycle. It should be cleared explicitly to break the reference cycle when the exception is no longer needed.

Storing `object` using a custom hook can resurrect it if it is set to an object which is being finalized. Avoid storing `object` after the custom hook completes to avoid resurrecting objects.

Raise an auditing event `sys.unraisablehook` with arguments _hook_, _unraisable_ when an exception that cannot be handled occurs. The _unraisable_ object is the same as what will be passed to the hook. If no hook has been set, _hook_ may be `None`.

New in version 3.8.

sys.version[](https://docs.python.org/3/library/sys.html#sys.version "Permalink to this definition")

A string containing the version number of the Python interpreter plus additional information on the build number and compiler used. This string is displayed when the interactive interpreter is started. Do not extract version information out of it, rather, use [`version_info`](https://docs.python.org/3/library/sys.html#sys.version_info "sys.version_info") and the functions provided by the [`platform`](https://docs.python.org/3/library/platform.html#module-platform "platform: Retrieves as much platform identifying data as possible.") module.

sys.api_version[](https://docs.python.org/3/library/sys.html#sys.api_version "Permalink to this definition")

The C API version for this interpreter. Programmers may find this useful when debugging version conflicts between Python and extension modules.

sys.version_info[](https://docs.python.org/3/library/sys.html#sys.version_info "Permalink to this definition")

A tuple containing the five components of the version number: _major_, _minor_, _micro_, _releaselevel_, and _serial_. All values except _releaselevel_ are integers; the release level is `'alpha'`, `'beta'`, `'candidate'`, or `'final'`. The `version_info` value corresponding to the Python version 2.0 is `(2, 0, 0, 'final', 0)`. The components can also be accessed by name, so `sys.version_info[0]` is equivalent to `sys.version_info.major` and so on.

Changed in version 3.1: Added named component attributes.

sys.warnoptions[](https://docs.python.org/3/library/sys.html#sys.warnoptions "Permalink to this definition")

This is an implementation detail of the warnings framework; do not modify this value. Refer to the [`warnings`](https://docs.python.org/3/library/warnings.html#module-warnings "warnings: Issue warning messages and control their disposition.") module for more information on the warnings framework.

sys.winver[](https://docs.python.org/3/library/sys.html#sys.winver "Permalink to this definition")

The version number used to form registry keys on Windows platforms. This is stored as string resource 1000 in the Python DLL. The value is normally the major and minor versions of the running Python interpreter. It is provided in the [`sys`](https://docs.python.org/3/library/sys.html#module-sys "sys: Access system-specific parameters and functions.") module for informational purposes; modifying this value has no effect on the registry keys used by Python.

[Availability](https://docs.python.org/3/library/intro.html#availability): Windows.

sys.monitoring

Namespace containing functions and constants for register callbacks and controlling monitoring events. See [`sys.monitoring`](https://docs.python.org/3/library/sys.monitoring.html#module-sys.monitoring "sys.monitoring: Access and control event monitoring") for details.

sys._xoptions[](https://docs.python.org/3/library/sys.html#sys._xoptions "Permalink to this definition")

A dictionary of the various implementation-specific flags passed through the [`-X`](https://docs.python.org/3/using/cmdline.html#cmdoption-X) command-line option. Option names are either mapped to their values, if given explicitly, or to [`True`](https://docs.python.org/3/library/constants.html#True "True"). Example:

$ ./python -Xa=b -Xc
Python 3.2a3+ (py3k, Oct 16 2010, 20:14:50)
[GCC 4.4.3] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys._xoptions
{'a': 'b', 'c': True}

**CPython implementation detail:** This is a CPython-specific way of accessing options passed through [`-X`](https://docs.python.org/3/using/cmdline.html#cmdoption-X). Other implementations may export them through other means, or not at all.

New in version 3.2.

Citations

[C99](https://docs.python.org/3/library/sys.html#id1)

ISO/IEC 9899:1999. “Programming languages – C.” A public draft of this standard is available at [https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf)local[[n1256.pdf]].