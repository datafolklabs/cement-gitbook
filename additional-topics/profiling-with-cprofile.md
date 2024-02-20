# Profiling with cProfile

Cement apps can be profiled with cProfile very easily, however there are some considerations as it may not work "out of the box" as expected. This doc comes as the output of the following issue on Github:

* [https://github.com/datafolklabs/cement/issues/547](https://github.com/datafolklabs/cement/issues/547)



{% hint style="info" %}
The following assumes that your Cement project was created from the developer tools; `cement generate project`. This is not necessary, however the use of `myapp.main` assumes the Cement App, and `main()` function live here. Modify to suit.



For your actual app, replace `myapp` with your Python package name for your app.
{% endhint %}



References:

* [https://docs.python.org/3/library/profile.html](https://docs.python.org/3/library/profile.html)
* [https://www.turing.com/kb/python-code-with-cprofile](https://www.turing.com/kb/python-code-with-cprofile)

### Profiling Commands

In order to profile specific commands within our app, we need to save cProfile to an output file. For basic example, we can run `myapp --help` with cProfile with the following, which will save our profile to the file `prof.dat`:

```
python -m cProfile -o prof.dat -m myapp.main --help
```



The output file should probably named appropriately based on what you are profiling. For example, if I need to profile `command1` I will save that to it's own profile output file.

```
python -m cProfile -o prof-command1.dat -m myapp.main command1
Inside MyApp.command1()
```



### Inspecting a Profile

Now that we have our commands profiled, we can inspect that same as any other program. Here we are opening up the `prof.dat` with `pstats`:

```
python -m pstats prof.dat
Welcome to the profile statistics browser.
prof.dat%
```

You might want to sort by "cumulative time" (`cumtime`) and then review the stats:

```
prof.dat% sort cumtime

prof.dat% stats

Tue Jan  2 03:07:11 2024    prof.dat

         150506 function calls (146817 primitive calls) in 0.202 seconds

   Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
    182/1    0.002    0.000    0.202    0.202 {built-in method builtins.exec}
        1    0.000    0.000    0.202    0.202 <string>:1(<module>)
        1    0.000    0.000    0.202    0.202 /usr/local/lib/python3.8/runpy.py:197(run_module)
        1    0.000    0.000    0.197    0.197 /usr/local/lib/python3.8/runpy.py:64(_run_code)
        1    0.000    0.000    0.197    0.197 /src/tmp/myapp/myapp/main.py:2(<module>)
   195/12    0.001    0.000    0.191    0.016 <frozen importlib._bootstrap>:986(_find_and_load)
   195/12    0.001    0.000    0.191    0.016 <frozen importlib._bootstrap>:956(_find_and_load_unlocked)
   186/14    0.001    0.000    0.185    0.013 <frozen importlib._bootstrap>:650(_load_unlocked)
   158/14    0.001    0.000    0.184    0.013 <frozen importlib._bootstrap_external>:837(exec_module)
   248/14    0.000    0.000    0.177    0.013 <frozen importlib._bootstrap>:211(_call_with_frames_removed)
    44/25    0.000    0.000    0.155    0.006 {built-in method builtins.__import__}
        1    0.000    0.000    0.107    0.107 /src/tmp/myapp/myapp/main.py:57(main)
        1    0.000    0.000    0.103    0.103 /src/cement/core/foundation.py:1733(__enter__)
        1    0.000    0.000    0.103    0.103 /src/cement/core/foundation.py:880(setup)
        1    0.000    0.000    0.100    0.100 /src/cement/core/foundation.py:1269(_setup_extension_handler)
        2    0.000    0.000    0.100    0.050 /src/cement/core/extension.py:135(load_extensions)
        9    0.000    0.000    0.100    0.011 /src/cement/core/extension.py:99(load_extension)
        1    0.000    0.000    0.069    0.069 /src/cement/__init__.py:4(<module>)
    76/55    0.000    0.000    0.065    0.001 <frozen importlib._bootstrap>:1017(_handle_fromlist)
        1    0.000    0.000    0.053    0.053 /src/cement/core/foundation.py:1(<module>)
      159    0.002    0.000    0.050    0.000 <frozen importlib._bootstrap_external>:909(get_code)
        1    0.000    0.000    0.040    0.040 /src/cement/ext/ext_jinja2.py:1(<module>)
        1    0.000    0.000    0.039    0.039 /usr/local/lib/python3.8/site-packages/jinja2/__init__.py:1(<module>)
      260    0.001    0.000    0.038    0.000 /usr/local/lib/python3.8/re.py:289(_compile)
  685/647    0.007    0.000    0.038    0.000 {built-in method builtins.__build_class__}
      103    0.000    0.000    0.037    0.000 /usr/local/lib/python3.8/re.py:250(compile)
        1    0.000    0.000    0.037    0.037 /usr/local/lib/python3.8/site-packages/jinja2/environment.py:1(<module>)
       83    0.000    0.000    0.037    0.000 /usr/local/lib/python3.8/sre_compile.py:759(compile)
      159    0.007    0.000    0.032    0.000 <frozen importlib._bootstrap_external>:1029(get_data)
      192    0.001    0.000    0.027    0.000 <frozen importlib._bootstrap>:890(_find_spec)
      188    0.000    0.000    0.024    0.000 <frozen importlib._bootstrap_external>:1399(find_spec)
      188    0.002    0.000    0.024    0.000 <frozen importlib._bootstrap_external>:1367(_get_spec)
        1    0.000    0.000    0.023    0.023 /src/cement/ext/ext_smtp.py:1(<module>)
        1    0.000    0.000    0.022    0.022 /src/cement/core/arg.py:1(<module>)
      321    0.003    0.000    0.021    0.000 <frozen importlib._bootstrap_external>:1498(find_spec)
      [SNIPPED]
```
