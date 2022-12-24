# vcpkg-action

`vcpkg-action` is a simple action to build and cache vcpkg packages. It supports all platforms. It has two unique
features:

* Simplicity
* Use of a "dry-run" build to generate a unique cache key for the configuration. This guarantees that if packages
  change, the cache will be rebuilt, but avoids rebuilding when it isn't necessary.
* Optionally supports reading `vcpkg.json` manifest files

`vcpkg` is cloned to the `${{ github.workspace }}\vcpkg` directory, and the build products are located in
 `${{ github.workspace }}\vcpkg\installed\<triplet>`. For cmake, use the option:

```
 -DCMAKE_TOOLCHAIN_FILE=${{ github.workspace }}/vcpkg/scripts/buildsystems/vcpkg.cmake -DVCPKG_TARGET_TRIPLET=<triplet> -DVCPKG_MANIFEST_MODE=OFF
```

Another directory named `vcpkg_cache` is created in the workspace root. This directory is used to store the cache files, 
and is cached using `pat-s/always-upload-cache@v3`. The cache key is automatically generated, 
but can also be modified using the `cache-key` argument.

Simple usage example:

```yaml
- name: vcpkg build
  uses: johnwason/vcpkg-action@v3
  with:
    pkgs: boost-date-time boost-system
    triplet: x64-windows-release
    token: ${{ github.token }}
```

Simple manifest example:

```yaml
- name: vcpkg build
  uses: johnwason/vcpkg-action@v3
  with:
    manifest-dir: ${{ github.workspace }} # Set to directory containing vcpkg.json
    triplet: x64-windows-release
    token: ${{ github.token }}
```


## Usage

```yaml
- uses: johnwason/vcpkg-action@v3
  with:
    # The vcpkg packages to build, separated by spaces. Cannot be used with manifest-dir
    pkgs: ''
    # The vcpkg target triplet to use. This must be set. For windows, 
    # x64-windows-release is recommended if you don't need debug libraries
    triplet: ''
    # Extra arguments to pass to vcpkg command (optional)
    extra-args: ''
    # Additional string to add to cache key. If using a build matrix or building different configurations
    # on the same operating system, be sure to include an additional cache key to separate the caches. (optional)
    cache-key: ''
    # vcpkg revision to checkout
    # A valid git ref; if empty, it defaults to the latest vcpkg stable release.
    revision: ''
    # GitHub token to authenticate API requests. This is necessary to determine vcpkg version to checkout
    # Recommended to use ${{ github.token }}
    token: ''
    # Directory containing vcpkg.json manifest file. Cannot be used with pkgs.
    manifest-dir: ''

```

## Advanced Example

The following is an advanced example with a matrix build. Note that the runner name is included as an additional
cache key.

```yaml
jobs:
  buildme:
    runs-on: ${{ matrix.config.os }}
    strategy:
      matrix:
        config:
        - os: ubuntu-20.04
          vcpkg_triplet: x64-linux-release
        - os: macos-11
          vcpkg_triplet: x64-osx-release
        - os: windows-2019
          vcpkg_triplet: x64-windows-release
    steps:
      - name: vcpkg build
        uses: johnwason/vcpkg-action@v3
        with:
          pkgs: boost-date-time
          triplet: ${{ matrix.config.vcpkg_triplet }}
          cache-key: ${{ matrix.config.os }}
          revision: master
          token: ${{ github.token }}
```

