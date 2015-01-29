![libuv][libuv_banner]

## Libuv 0.10.33 (stable)

The source code if this block (entirely) is on this github fork of libuv (on branch **v0.10.29_biicode**)

[https://github.com/lasote/libuv](https://github.com/lasote/libuv)

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
    lasote/libuv(v0.10): 1


Program your code and build it: 

    > bii cpp:build # This command will build your project and the libuv dependency


You can check an [the example](http://www.biicode.com/examples/examples/libuv/v0.10) with a client/server application using this block.

[libuv_banner]: https://raw.githubusercontent.com/libuv/libuv/master/img/banner.png

