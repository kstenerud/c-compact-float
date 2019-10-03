Reference Implementation for Compact Float
==========================================

A C implementation of [compact float](https://github.com/kstenerud/compact-float/blob/master/compact-float-specification.md).



Assumptions
-----------

 * Internal rounding and conversions use decimal floats (`_DecimalXYZ` in C, `std::decimal::decimalXYZ` in C++) under the assumption that host encoding is binary integer decimal (BID). This is the default for most compilers using `decNumber` (I think IBM is the only exception?). If host encoding is densely packed decimal (DPD), results will be incorrect. BTW, I'd appreciate a PR with a DPD version of `extract_float()` ;-)



Usage
-----

```cpp
#include <stdio.h>
#include <vector>
#include <compact_float/compact_float.h>

static void print_buffer(const uint8_t* data, int length)
{
    for(int i = 0; i < length; i++)
    {
        printf("%02x ", data[i]);
    }
    printf("\n");
}

static void demonstrate_encode()
{
    double value = -3.23828125;
    int size = cfloat_binary_encoded_size(value);
    std::vector<uint8_t> data(size);
    int encoded_count = cfloat_binary_encode(value, data.data(), data.size());
    if(encoded_count <= 0)
    {
        // TODO: Error
    }
    print_buffer(data.data(), data.size()); // Prints 05 a0 4f
}
```

Output:

    05 a0 4f


```cpp
static void demonstrate_decode()
{
    std::vector<uint8_t> data = {0x04, 0xc0, 0xb0, 0x0f};
    double value = 0;
    int decoded_count = cfloat_binary_decode(data.data(), data.size(), &value);
    if(decoded_count <= 0)
    {
        // TODO: Error
    }
    printf("%.20g\n", value); // Prints 2.24029541015625
}
```

Output:

    2.24029541015625



Requirements
------------

  * Meson 0.49 or newer
  * Ninja 1.8.2 or newer
  * A C compiler
  * A C++ compiler (for the tests)



Building
--------

    meson build
    ninja -C build



Running Tests
-------------

    ninja -C build test

For the full report:

    ./build/run_tests



Installing
----------

    ninja -C build install
