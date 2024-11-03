libexdupe is a compression library that also performs data deduplication.

It finds identical sequences of data as small as 4 KB across terabytes of input data at a speed of up to **5 gigabyte per second**<sup>*</sup>.

It has a simple C++ interface - all you need to compress from stdin to stdout is following:
```
int main() {
    size_t mb = 1 << 20;
    vector<char> in;
    vector<char> out;
    compressor::init(8, 200 * mb, 1);

    while (std::cin.peek() != EOF) {
        size_t len = fread(in.data(), 1, mb, stdin);
        compressor::compress(in, out);
        fwrite(out.data(), 1, out.size(), stdout);
    }

    compressor::flush(out);
    fwrite(out.data(), 1, out.size(), stdout);
    fflush(stdout);
}
```
It's currently just a preview version - use for testing only. You can use tar for that:

```tar cvf - all_my_files | ./demo -c > archive.demo```

Note that only certain kinds of data will benefit from deduplication. Below is a benchmark of the backup tool [eXdupe](https://exdupe.net/) which uses libexdupe to compress a Linux virtual machine of 22.1 GB:

|                | eXdupe 3 | tar+zstd | kopia | restic | Duplicacy | zpaq64 | 7-Zip flzma2 | Duplicati |
|----------------|--------|----------|-------|--------|-----------|--------|--------------|-----------|
| **Time**           | 9.76 s | 14.2 s   | 14.8 s | 24.8 s | 77.0 s    | 112 s  | 209 s        | 360 s     |
| **Size**          | 7.34 GB| 10.6 GB  | 9.93 GB| 9.21 GB| 11.4 GB   | 8.18 GB| 9.42 GB      | 10.2 GB   |

<sup>*</sup>You can reach 5 gigabyte per second when using libexdupe directly (binding.hpp performs memcpy() operations) when in-memory at compression level 0 on 4 threads.
