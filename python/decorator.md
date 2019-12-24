
## Decorator Examples

Decorator example with defined time function 

Simple timer function example
``` python
def timer(func):
    def wrapper():
        start_time = time.perf_counter()   
        value = func()   # call function 
        end_time = time.perf_counter()     
        run_time = end_time - start_time   
        print(f"Finished {func.__name__!r} in {run_time:.4f} secs")
    return wrapper
```

``` python 
def computation_func():
    print('computation done')
```
decorate the function without using @sign

```python 
comp_rst = computation_func()
comp_rst()
```


decorate function with @sign
```python 
@timer
def computation_func():
    print('computation done')
```


```python 

```
```python 

```
```python 

```

```python 

```
```python 

```