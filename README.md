#vcpkg-action

`vcpkg-action` is a simple action to build and cache vcpkg packages. It currently only supports Windows. `vcpkg` is cloned in the `${{ github.workspace }}\vcpkg` directory, and the build products are located in `${{ github.workspace }}\vcpkg\installed\<triplet>`. For cmake, use the option:

```
 -DCMAKE_TOOLCHAIN_FILE=${{ github.workspace }}/vcpkg/scripts/buildsystems/vcpkg.cmake
        -DVCPKG_TARGET_TRIPLET=<triplet>
```

Another directory named `vcpkg_cache` is created in the workspace root. This directory is used to store the cache files, and is cached using `pat-s/always-upload-cache@v3`. The cache key is automatically generated, but can also be modified using the `cache-key` argument.

Example usage:

```yaml
- name: vcpkg build
  uses: johnwason/vcpkg-action@v1
  with:
    pkgs: boost-date-time boost-system
    triplet: x64-windows-release
```

## Usage

```yaml
- uses: johnwason/vcpkg-action@v1
  with:
    # The vcpkg packages to build, separated by spaces
    pkgs: ''
    # The vcpkg target triplet to use. This must be set. For windows, 
    # x64-windows-release is recommended if you don't need debug libraries
    triplet: ''
    # Extra arguments to pass to vcpkg command (optional)
    extra-args: ''
    # Additional string to add to cache key (optional)
    cache-key: ''

```
