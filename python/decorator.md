
## Decorator Examples

Decorator example with a simple time function 

Simple timer function example
``` python
def timer(func):
    def wrapper():
        start_time = time.perf_counter()   
        value = func()   # call the actual function 
        end_time = time.perf_counter()     
        run_time = end_time - start_time   
        print(f"Finished {func.__name__!r} in {run_time:.4f} secs")
    return wrapper
```

A function to be decorated
``` python 
def computation_func():
    print('computation done')
```
Decorate the function without using @sign

```python 
comp_rst = computation_func()
comp_rst()
```


Decorate the function with @sign
```python 
@timer
def computation_func():
    print('computation done')
```

Decorating functions with Parameters

A simple addition funcion 
```python 
def addition(list_values):
    return [x**2 for x in list_values]
```
Addition function takes two parameters, and it will throws error with the orginal timer function because paramaters are not defined

Make sure to take arguments in the wrapper function 
```python 
def timer(func):
    def wrapper(*args, **kwargs):
        start_time = time.perf_counter()   
        value = func(*args, **kwargs)   # call the actual function 
        end_time = time.perf_counter()     
        run_time = end_time - start_time   
        print(f"Finished {func.__name__!r} in {run_time:.4f} secs")
    return wrapper
```
```python 

```

```python 

```
```python 

```