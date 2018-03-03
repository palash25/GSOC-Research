# GSOC-Research

## Oragnization in focus
[coala](https://github.com/coala/coala)
[Project Link](https://projects.coala.io/#/projects?project=optimize_caching&lang=en)

## Potential Bottlenecks
- The use of `cPickle` instead of `pickle` in `CachingUtilities.py` for object serialization can potentially boost performance significantly for large projects.

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
- [Nextgen Core Integation](https://github.com/coala/coala/issues/4348)
- [Cache control](https://github.com/coala/coala/issues/5136)
- [Nextgen Core: Caching](https://github.com/coala/coala/issues/4350)


## Notes
### Performance optimization
- Loading bears takes times and can be improved using cib. Reference: [this issue](https://github.com/coala/coala/issues/2912#issuecomment-261551204)
- Bear loading needs to be improved
- `yield_once` takes a lot of CPU time. It can be found in **coala_utils.decoraters**, **Collectors.py**, **Importers.py** and **Globbing.py**. Performance can be optimized by not using `yield_once` in cases where we expect all distinct results (without any duplication) will be yielded.
- Significant files and packages of interest **coalib.misc.CachingUtilities**, **coalib/processes/Processing.py**, **coalib/misc/Caching.py**  and **coala_main.py**.
- Need to read **RELEASE_NOTES.rst** and **coalib/parsing/DefaultArgParser.py**.
- Need to look at some standard libraries that might be useful: **hashlib**, **pickle**, etc.

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

### Additional points to include in the proposal
- A benchmarking tool using VMProf API or low level profiling and a custom flag for coala to run analysis and option to check for bears individually and Integrate it with CIs used by coala. See [issue](https://github.com/coala/coala/issues/3914)

### General Resources
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
