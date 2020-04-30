# Performance Guide

## Overview

### The process of optimization

```python
def optimize():
    """Recommended optimization"""
    assert got_architecture_right(), "fix architecture" 
    assert made_code_work(bugs=None), "fix bugs"
    while code_is_too_slow():
        wbn = find_worst_bottleneck(just_guess=False, profile=True)
        is_faster = try_to_optimize(wbn, run_unit_tests=True, new_bugs=None)
        if not is_faster:
           undo_last_code_change()
# By Stefan Schwarzer, Europython 2006
``` 

### Three rules of optimization

* Make it work first
* Work from the user's point of view
* Keep the code readable

## Profile & benchmark

This part is finding bottleneck, and done by:  

* profile CPU usage
* profile memory usage
* Profile network usage

### profile CPU usage

#### Macro-profiling (whole program profile and generates statistics)

* profile

* cProfile


#### Micro-profiling (part of program profile)

* `profile()`decorator

* `timeit`

#### `Pystones`

### profile memory usage

#### GC and common pitfall

##### argument inbound outbound edge-case

Do *NOT* alloc the mutable object at arguments, this object may cause unexpected object lifecycle and wire result 

*Bad*

```python

def foo(bar={}):
    if 1 in bar:
        bar[1] = 2
    bar[3] = 4
    return bar

```

*good*

```python
def foo(bar=None):
    if not bar:
        bar = {}
    if 1 in bar:
        bar[1] = 2
    bar[3] = 4
    return bar


```

##### Common memory eater

* Caches that grow uncontrolled

* Object factories that register instances globally and do not keep track of their usage

* Threads that are not properly finished

* Objects with a `__del__` method and involved in a cycle

#### profiling memory

##### OS level

##### tools

###### `objgraph`

### profile network usage


* tools that watch the network traffic 

    * `ntop`

    * `wireshark`

* Track down unhealthy or mis-configured device
    
    * `net-snmp`
        [net-snmp](http://www.net-snmp.org/)
        

## Optimization

