# gl3w: Simple OpenGL core profile loading

Source: https://github.com/skaslev/gl3w

## Usage - pre-generated profile
This repository contains a generated profile that you can use directly. If you use a CMake build system, simply `add_subdirectory` this repository, otherwise make sure to add `src/gl3w.c` and `include/` to your build system. 

## Usage - generate a profile
This requires Python 2.7 or newer. It is also compatible with Python 3.x.

When adding this repository to your build system, define the flag `GL3W_GENERATE`. For a standalone build, do the following:

```
mkdir build
cd build
cmake -DGL3W_GENERATE=On ..
cmake --build .
```

## Usage - update this repository
If you wish to update this repository with the latest generated profile, run the commands above, then copy and replace the files from `/include` and `/src` in the FetchContent dependencies folder. By default, that would be `build/_deps/gl3w-src`. 

You may wish to manually run the `gl3w_gen.py` script in order to easily pass arguments, such as the OpenGL version and extensions. By default, the script will be in `build/_deps/gl3w-src/gl3w_gen.py`. 

## License
The code in this repository is available under Apache 2.0 license, unless otherwise noted by individual file headers. See [LICENSE](LICENSE) and [NOTICE](NOTICE.txt) files for more information.
