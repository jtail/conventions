### Performance Optimizations

This section covers rules that apply to any performance optimizations that sacrifice readability or maintainability.


#### Avoid premature optimization

Performance optimization must not be performed without explicit requirements, which include, at minimum:

1. Specifications of hardware on which performance must be demonstrated.
2. Definitions of quantitative input complexity metrics. Example: number of http requests per second, request size. 
3. Definitions of performance output metrics and their expected dependency. Optimizing for troughput and latency are 
 different tasks and even optimizing for maximum and average latency can be different. 

The above requirements must be documented explicitly.
Optimization should only occur, if existing code does not fulfill them.

Motto: Performance optimization, even when done in the right way, incurs extra maintenance costs, but has a fixed cost
to implement. It is an obvious economical decision to avoid ongoing costs as long as possible.  


#### Performance optimized code must be isolated

Code being optimized must be isolated in appropriate language construct - method, class or package.

Motto: In many cases, after performing the isolation it will be obvious that instead of optimizing a specific piece of
code, structural adjustments should be made to avoid calling it multiple times. For example, instead of optimizing 
configuration reader, loading it once and then passing parsed configuration might be a better solution.
Another good reason is maintaining ability to do comparative testing between optimized and non-optimized versions. 


#### Performance optimized code must be covered by tests 

Code being optimized must be tested for correctness more tenaciously than an average feature. 

Motto: Peformance optimizations often break functionality in subtle ways and the impact of errors can be quite high as
the code only starts to have performance requirements when it is heavily used.

Note: Exhaustive tests are hard to maintain without proper tools that support parameterized testing. Consider investing 
time into adopting a test framework such as Spock, JUnit Theories or JUnit 5 if you are not using one already.   


#### Keep the non-optimized version of code

Version of optimized code that does not contain performance optimizations must be kept in the source codebase.
The exact same correctness tests should be run against both optimized and non-optimized versions of code.

Motto: When investigating issues in optimized code, it is a common need to compare the output produced by optimized and
non-optimized code to verify if the issue is design error or was introduced in the optimized version. This should be as
trivial as adding an extra test case.


#### Performace optimizations must be benchmarked

The minimum requirements for benchmark are as follows:
1. Runnable via the build system command line switch.
2. Measure dependency of each performance output metric on each performance input metric.
3. Measure both optimized and non-optimized version of code.

Note: As benchmarks are often time-consuming and resource sensitive, it is rarely appropriate to run it during normal 
continuous integration cycle. In the lack of dedicated hardware, scheduling nightly benchmark runs when other builds
are disabled is often a cost-effective solution, that still is reproducible enough. 