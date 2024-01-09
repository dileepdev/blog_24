[ref](https://dev.to/derlin/diving-deeper-into-python-exceptions-cf1)

# Chaining exception

## implict

```python
try:
    1/0
except Exception:
    raise RuntimeError("oops")
```

we got output something like this

```bash
ERROR!
Traceback (most recent call last):
  File "<string>", line 4, in <module>
ZeroDivisionError: division by zero

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<string>", line 6, in <module>
RuntimeError: oops
```

## explit

manage manuly for chaining

```python
try:
    1/0
except Exception as e:
    raise RuntimeError("oops") from e
```

so the output now changed into direct clause

```bash
Traceback (most recent call last):
File "<string>", line 2, in <module>
ERROR!
ZeroDivisionError: division by zero
The above exception was the direct cause of the following exception:
Traceback (most recent call last):
File "<string>", line 4, in <module>
ERROR!
RuntimeError: oops
>
```

## **context**

**suppress_context** is false by default.

When raising a new exception while another exception is already being handled (in except, finally or with), the new exception’s **context** attribute is automatically set to the handled exception.

When using raise ... from , the supplied exception will additionally be saved in the **cause** attribute of the raised exception, and **suppress_context** will be set to true.

The default traceback uses those attributes to display stacktraces in the following way:

if **cause** is present, always show it

if **cause** is None, show the **context** only if **suppress_context** is false.

```python
def divZero(n):
    try:
        return n/0
    except Exception as e:
        raise RuntimeError("oops")

try:
    divZero(10)
except Exception as e:
    print(e)

try:
    divZero(10)
except Exception as e:
    print(e.__context__.args[0])

```

this output as below

```bash
oops
division by zero
```

## Warnig in python

```python
import warnings
def f():
    warnings.warn("deprecated warning", DeprecationWarning)
    warnings.warn("user warning")
# expect to print deprecated warning
f()
# second time warning is not printing
f()

```

output

```bash
<string>:3: DeprecationWarning: deprecated warning
<string>:4: UserWarning: user warning
```

## DON'T USE bare except

use exception if needed

## add_note in python 3.11

```python
def divZero(n):
    try:
        return n/0
    except Exception as e:
        e.add_note("this is not expecting")
        raise

try:
    divZero(10)
except Exception as e:
    print(e)

try:
    divZero(10)
except Exception as e:
    print(e.__context__.args[0])
```

output

```bash
division by zero
ERROR!
Traceback (most recent call last):
  File "<string>", line 14, in <module>
  File "<string>", line 3, in divZero
ZeroDivisionError: division by zero
this is not expecting

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<string>", line 16, in <module>
AttributeError: 'NoneType' object has no attribute 'args'
```
