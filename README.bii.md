![libuv][libuv_banner]


## Libuv 1.4.2 (stable)

[![Biicode block](https://webapi.biicode.com/v1/badges/lasote/lasote/libuv/v1.x)](https://www.biicode.com/lasote/lasote/libuv/v1.x) 


Linux GCC:
[![Build Status](https://travis-ci.org/lasote/libuv-1.svg?branch=v1.x_biicode)](https://travis-ci.org/lasote/libuv-1)


Windows Visual Studio:
[![Build status](https://ci.appveyor.com/api/projects/status/th4i98wk3jkrfuvg/branch/v1.x_biicode?svg=true)](https://ci.appveyor.com/project/lasote/libuv-1/branch/v1.x_biicode)


The source code if this block (entirely) is on this github fork of libuv (on branch **v1.x_biicode**)


## How to use it?

New with biicode? Check the [getting started guide](http://docs.biicode.com/c++/gettingstarted.html).

Create new biicode project and create an empty block:
    
    > bii init myproject
    > bii new myuser/myblock


Include **uv.h** from this block in your source code:

    #include "lasote/libuv/include/uv.h"


Open **biicode.conf** file and put a requirement to this block:

    [requirements]
    # This file contains your block external dependencies references
    lasote/libuv(v1.x): 3


Program your code and build it: 

    > bii cpp:build # This command will build your project and the libuv dependency


You can check an [the example](http://www.biicode.com/examples/examples/libuv/v1.0) with a client/server application using this block.

[libuv_banner]: https://raw.githubusercontent.com/libuv/libuv/master/img/banner.png

