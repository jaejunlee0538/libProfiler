libProfiler
===========


libProfiler is a tool to measure time taken by a code portion. It can help you improve code performance.
It can be easily integrated in your toolchain, continuous integration,...
It's implemented as only one multiplatform and threadsafe C++ header file! It works on Windows, Linux and MacOSX.
As you define the granularity, it may be more usefull than great tools like Verysleepy. And it
works well with debug info turned off and full optmisation turned on.
Sadly, it uses STL (nobody's perfect)

How to use it:

Include this header. For one of .cpp where it's included add:

    #define LIB_PROFILER_IMPLEMENTATION

In your project preprocessor, define USE_PROFILER. If USE_PROFILER is not defined, every
libProfiler macro is defined empty. So depending on your project target, you can enable/disable
profiling.

Let's see an example code :


    #include <iostream>
    #include <math.h>
    #include <stdio.h>
    void myPrintf( const char *szText )
    {
        printf("%s", szText);
    }


    #define USE_PROFILER 1
    #define LIB_PROFILER_IMPLEMENTATION
    #define LIB_PROFILER_PRINTF myPrintf
    #define LOG_ELAPSED_TIME_HISTORY 1
    #include "libProfiler.h"


    void myFunction()
    {
        PROFILER_START(myFunction);
        float v = 0;
        for(int i = 0;i<1000000;i++)
            v += cosf(static_cast<float>(rand()));

        printf("v = %5.4f\n", v);
        PROFILER_END();
    }

    int main(int argc, const char * argv[])
    {
        PROFILER_ENABLE;
        
        PROFILER_START(Main);

        std::cout << "Hello, World!\n";
        for(int i=0;i<20;i++){
            myFunction();
        }

        PROFILER_END();

        LogProfiler();
        
        PROFILER_DISABLE;
        
        return 0;
    }


Enable/disable profiling with USE_PROFILER.
In one of your .cpp file, define LIB_PROFILER_IMPLEMENTATION or you'll have troubles linking.
You can overide the default printf output redefining preprocessor LIB_PROFILER_PRINTF.

Without `LOG_ELAPSED_TIME_HISTORY` being defined as `1`, the sample will output:

    CALLSTACK of Thread 0
    _______________________________________________________________________________________
    | Total time   | Avg Time     |  Min time    |  Max time    | Calls  | Section
    _______________________________________________________________________________________
    |     756.9800 |     756.9800 |     756.9800 |     756.9800 |     1  | Main
    |     756.8438 |      37.8422 |      38.9700 |      38.9700 |    20  |   myFunction
    _______________________________________________________________________________________



Defining `LOG_ELAPSED_TIME_HISTORY` as `1` will enable you to log entire time history of each `PROFILER_START - PROFILER_END` blocks.

    CALLSTACK of Thread 0
    _______________________________________________________________________________________
    | Total time   | Avg Time     |  Min time    |  Max time    | Calls  | Section
    _______________________________________________________________________________________
    |     758.8989 |     758.8989 |     758.8989 |     758.8989 |     1  | Main
    |     758.7920 |      37.9396 |      38.4761 |      38.4761 |    20  |   myFunction
    _______________________________________________________________________________________



    _______________________________________________________________________________________
    Time history of Thread 0

    @Main:
    758.899

    @Main|myFunction:
    38.476, 38.545, 37.950, 37.730, 38.246, 37.879, 37.896, 37.818, 38.295, 37.901, 37.980, 37.761, 37.759, 37.803, 37.882, 38.163, 37.731, 37.557, 37.522, 37.898

    _______________________________________________________________________________________


Time unit is ms.
    
This text is also present in libProfiler.h

This has been possible thank to the work of Christophe Giraud and Maxime Houlier.
