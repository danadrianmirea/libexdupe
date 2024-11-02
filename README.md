libexdupe is a compression library that also performs data deduplication.

It finds identical sequences of data as small as 4 KB across terabytes of input data at a speed of **5 gigabytes per second**<sup>*</sup> with 4 threads.

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

<sub><sup>*</sup>Level 0 and bypassing binding.hpp because it performs memcpy() operations.</sub>
