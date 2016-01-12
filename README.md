# Dependencies for Image Work (zlib + libpng)

Trivial system to setup libpng + zlib.  I'm putting this here, despite it being completely trivial, to make it easier for people who aren't used to CMake, or for me when I'm feeling lazy.

Note: *export is disabled to stop libpng freaking out (because zlib doesn't by default)*

## Building

### Git

    # Fetch
    git clone https://github.com/awstanley/deps-zlib-libpng
    cd deps-zlib-libpng
    git submodule init
    git submodule update --init --recursive && git submodule update --remote


### Setting up the build

    # Build out of the tree
    cd ..
    mkdir build-deps-zlib-libpng # or whatever suits
    cd build-deps-zlib-libpng
    cmake ../deps-zlib-libpng

Then use your toolchain as normal.

## Updating

From within the project:

    git submodule update --init --recursive && git submodule update --remote

If you are embedded within another project, the above command will work if it is a git repository.

## Use

Options are exposed as they are provided; zlib always build static and dynamic, mind your linkage.

## Embedding

Embedding is trivial.  If this project is not top/root, it will feed its values up.

## Sample

There is no sample for this; it's nothing complex and most people using these libraries for game development do so through another interface (usually `SFML`, `SDL` (via `SDL_image`), or similar).

## Licence

As I've been asked, consider this public domain (it's just a CMakeLists file!); licences for libraries used obviously vary, and at the time of writing they are both under the zlib/png.  (Check respective `LICENCE` or `COPYING` files).

If public domain isn't available for you (for whatever legal reason), consider this under zlib/png.