
## Decorator Examples

Decorator example with a simple timer function 

Simple timer function:
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

### Decorate the function without using @sign
```python 
comp_rst = timer(computation_func)
comp_rst()
```


### Decorate the function with @sign
```python 
@timer
def computation_func():
    print('computation done')
```

### Decorating functions with Parameters

A square function that takes square from each element in a list
```python 
@timer
def square(list_values):
    rst = [x**2 for x in list_values]
    print(rst)
```
SInce square function takes a parameter, it will throws an error with the orginal timer function because paramaters are not defined in wrapper function. 

Make sure to take arguments in the wrapper function using *args, **kwargs or match arguments defined in square function. 
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


### Returning values from decorated function

A function with return value.
```python 
@timer
def square(list_values):
    rst = [x**2 for x in list_values]
    return rst
```

Corresponding timer function to a function with return value.
```python 
def timer(func):
    def wrapper(*args, **kwargs):
        start_time = time.perf_counter()   
        value = func(*args, **kwargs)   # call the actual function 
        end_time = time.perf_counter()     
        run_time = end_time - start_time   
        print(f"Finished {func.__name__!r} in {run_time:.4f} secs")
        return value
    return wrapper

```

### Preserving information about the original function

```python 
print(square.__name__) # 'wrapper' which shoud be 'square'
```

Use functools.wraps decorator to preserve information about the original function. 

The @functools.wraps decorator uses the function functools.update_wrapper() to update special attributes like __name__ and __doc__ that are used in the introspection.

``` python
import functools

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.perf_counter()   
        value = func(*args, **kwargs)   # call the actual function 
        end_time = time.perf_counter()     
        run_time = end_time - start_time   
        print(f"Finished {func.__name__!r} in {run_time:.4f} secs")
        return value
    return wrapper

@timer
def square(list_values):
    rst = [x**2 for x in list_values]
    return rst

print(square.__name__) # 'square'    
```

### Nested deocorators
```python 
def star(func):
    def inner(*args, **kwargs):
        print("*" * 30)
        func(*args, **kwargs)
        print("*" * 30)
    return inner

def percent(func):
    def inner(*args, **kwargs):
        print("%" * 30)
        func(*args, **kwargs)
        print("%" * 30)
    return inner

@star
@percent
def printer(msg):
    print(msg)
printer("Hello")
```
Gives the output:

```
******************************
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
Hello
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
******************************
```

Above systnax is equivalent to the following
```python
printer = star(percent(printer))
printer()
```