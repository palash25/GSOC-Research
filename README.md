# GSOC-Research

## Oragnization in focus
- [coala](https://github.com/coala/coala)
- [Project Link](https://projects.coala.io/#/projects?project=optimize_caching&lang=en)

## Potential Bottlenecks And Related Issues To Fix
- ~~The use of `cPickle` instead of `pickle` in `CachingUtilities.py` for object serialization can potentially boost performance significantly for large projects.~~ Default in python 3.
- Find a reliable hash for collection-types that don't guarantee an order (e.g. dict, set). [issue link](https://github.com/coala/coala/issues/5188)
- [Tracking directories](https://thomassileo.name/blog/2013/12/12/tracking-changes-in-directories-with-python/)
- [glob pattern match issue](https://github.com/coala/coala/issues/2300)

## Useful Resources/Links

### Project Specific
#### cEP
[cEP-002](https://github.com/coala/cEPs/blob/master/cEP-0002.md)

#### Performance Optimization Project
- [Performance optimization Thread](https://github.com/coala/coala/issues/2912)
- [BSCO Thread](https://github.com/coala/coala/issues/3788)
- [coala-perf-test thread](https://github.com/coala/coala/issues/3914)
- [adtac application](https://github.com/coala/teams/issues/3)

#### Debugger and Profiler Integration Project
- [Profiler Issue](https://github.com/coala/coala/issues/565)
- [Debugger Issue](https://github.com/coala/coala/issues/1101)

### Nextgen Core
- [Makman commits](https://github.com/coala/coala/commits/master?author=Makman2)
- [Nextgen-Core Milestone](https://github.com/coala/coala/milestone/18)
- [Nextgen Core Integation](https://github.com/coala/coala/issues/4348): Important for closing this issue: Writing extensive tests testing out all main features of the new core relevant for coala-users by invoking coala itself, especially for caching (different bear types, different file scenarios).
- [Cache control](https://github.com/coala/coala/issues/5136): Providing caching flags
  - `--cache-strategy / --cache-protocol`: Controls how coala manages caches for the next run.
    1. none: Don't use a cache at all. A shortcut-flag could be additionally implemented, `--no-cache`, effectively meaning `--cache-protocol=none`
    2. `primitive`: Use a cache that grows infinitely. All cache entries are stored for all following runs, and aren't removed. Effective when many recurrent changes happen in coafiles and settings. Fastest in storing.
    3. `lri`/`last-recently-used`(default flag): Cached items persist only until the next run. *Stretch issue: Implement count-parameters that allow to control when to discard items from the cache, e.g. after 3 runs of coala without using a cached item, discard it.*
    4. `--clear-cache`: Clears the cache.
    5. `--export-cache / --import-cache`: Maybe useful to share caches. Like CI server for any project run coala, and you can download the cache from there as an artifact to speed up your builds / coala runs.
    6. `--cache-compression`: Accepts as arguments:
      - none: No cache compression. This is default.
      - Other flags that specify common compression capabilities Python provides (for example lzma or gzip). *Cache compression should be evaluated before regarding its effectiveness, because the cache will mainly store hashes which usually aren't really redundant, the gain might be very low. The little performance penalty when loading the cache might be too much when respecting a possible very low gain of cache space reduction.*
    7. `--optimize-cache` A little performance penalty to make the cache loading faster. Particularly this feature shall utilize pickletools.optimize. But this is not exclusive to this flag.
    
- [Nextgen Core: Caching](https://github.com/coala/coala/issues/4350)


## Notes
### Performance optimization
- Loading bears takes times and can be improved using cib. Reference: [this issue](https://github.com/coala/coala/issues/2912#issuecomment-261551204)
- Bear loading needs to be improved
- `yield_once` takes a lot of CPU time. It can be found in **coala_utils.decoraters**, **Collectors.py**, **Importers.py** and **Globbing.py**. Performance can be optimized by not using `yield_once` in cases where we expect all distinct results (without any duplication) will be yielded.
- Significant files and packages of interest **coalib.misc.CachingUtilities**, **coalib/processes/Processing.py**, **coalib/misc/Caching.py**  and **coala_main.py**.
- Need to read **RELEASE_NOTES.rst** and **coalib/parsing/DefaultArgParser.py**.
- Need to look at some standard libraries that might be useful: **hashlib**, **pickle**, etc.

### Caching
- There are 3 steps involved in caching:
  1. Populating the cache 
  2. Keepig the cache in sync
  3. Managing the cache size

- Population processes are of 2 kinds:
  1. Upfront (when we know of all the data that we want to cache before hand)
  2. Lazy (cache as per the needs with an initial check for possible duplicates)
  
**Note:** Lazy population will take less initial cache build time than upfront but it still might cause one-off delays if there
  are checks in place for pre-existing cached objects (which might not be there at all)
  
- Cahce size management: These are the approaches for cache eviction
    1. Time based eviction: Either keep a separate thread for this (costly approach) or evict data at the time of reading it.
    2. First in, first out (FIFO)
    3. First in, last out (FILO).
    4. Least accessed (not recommende since old values are accessed more)
    5. Least time between access: When a value is accessed the cache marks the time the value was accessed and increases the access count. When the value is accessed the next time, the cache increments the access count, and calculates the average time between all accesses. Values that were once accessed a lot but fade in popularity will have a dropping average time between accesses. Sooner or later the average may drop low enough that the value will be evicted *(seems costly)*.
    
### Nextgen Core
- A Future is an object that doesn't have a result yet and is returned and handled by the executors used inside the core, while the core refers to tasks as (args, kwargs) objects that bears can pass to offload work into the core.

#### Previous performance issues and their fixes
This will provide some reference as to how caching works and is implemented

| Issue | Corresponding PR |
|-------|---------------|
| [caching affects other sections](https://github.com/coala/coala/issues/2708) | [Caching: Compute hash only for enabled sections](https://github.com/coala/coala/pull/2712) |
| [Improve caching to not explore directories that haven't been modified](https://github.com/coala/coala/issues/2541) | [Add file caching and remove Tagging](https://github.com/coala/coala/pull/2016) |

#### Other related issues
- [Improve caching to not explore directories that haven't been modified](https://github.com/coala/coala/issues/2541)
- [BSCO](https://github.com/coala/coala/issues/3788)
- [coala-utils: Lazy decorater](https://gitlab.com/coala/coala-utils/issues/3)
- [Document Nextgen Core](https://github.com/coala/coala/issues/4584)

### Additional points to include in the proposal
- A benchmarking tool using VMProf API or low level profiling and a custom flag for coala to run analysis and option to check for bears individually and Integrate it with CIs used by coala. See [issue](https://github.com/coala/coala/issues/3914)

### General Resources
#### Libraries
- [Stackoverflow](https://stackoverflow.com/questions/34116942/how-to-cache-asyncio-coroutines#34123396)
- functiools lru cache
- [Async Caching](https://alastaircrabtree.com/cache-the-result-of-an-async-method-using-lazycache/)
- [Quora Eng Blog Case Study](https://engineering.quora.com/Asynchronous-Programming-in-Python)
- [Multiprocess Pool vs asyncio.run_in_executor](https://stackoverflow.com/questions/45618893/multiprocess-pool-vs-asyncio-run-in-executor#45640104)
- [Caching](https://www.wikiwand.com/en/Cache_(computing))
- [Memoization](https://www.wikiwand.com/en/Memoization)
- [Detecting bottlenecks using static code analysis](https://dzone.com/articles/detecting-performance-bottlenecks)
- [Python profilers documentation](https://docs.python.org/2/library/profile.html)
- [Profiling Python Like a Boss](https://zapier.com/engineering/profiling-python-boss/)
- [Stackoverflow discussion on profiling](https://stackoverflow.com/questions/582336/how-can-you-profile-a-script)
- [Cyclomatic Complexity](https://www.wikiwand.com/en/Cyclomatic_complexity)
- [How to profile memory usage in Python ](https://www.pluralsight.com/blog/tutorials/how-to-profile-memory-usage-in-python)
- [Debugging and Profiling](https://docs.python.org/3/library/debug.html)
- [Physical Modeling With Python ](https://physicalmodelingwithpython.blogspot.in/2015/09/speeding-up-python-part-1-profiling.html)
- [Go faster Python](https://alimanfoo.github.io/2017/01/23/go-faster-python.html)
- [Optimizing Python code performance with cProfile](https://blog.alookanalytics.com/2017/03/21/python-profiling-basics/)
- [Python Speed Performance Tips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips)
- [Codementor Python Optimization](https://www.codementor.io/python/tutorial/stack-overflow-martijn-pieters-python-optimization)
- [Expert Python Programming E-book](https://www.packtpub.com/mapt/book/application_development/9781785886850)
- [OWASP article on static code analysis](https://www.owasp.org/index.php/Static_Code_Analysis)

### TODO
- Lookup cahching techniques
