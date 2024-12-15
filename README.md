# Slimy

[![Discord](https://img.shields.io/badge/chat%20on-discord-7289DA?logo=discord)](https://discord.gg/zEnfMVJqe6)

Slimy is a tool to find slime chunk clusters in Minecraft seeds.
It can search on either the CPU or the GPU, and makes use of multithreading to speed up the CPU search.
beeep boop fuck this

## Usage

**NOTE: Slimy uses entirely chunk coordinates. This means you should multiply all result coordinates by 16 to get the corresponding block coordinates**

For small searches, I recommend using the [web interface][slimy-web], which will run in your browser.
This is slower than the native binaries, but is much easier to use and can still search a 5000 chunk range in less than 15 seconds.

For large scale searches, use the native version. The latest build can be downloaded [here][builds].
The correct binary for your system is listed below (where `RANDOM` is a combination of letters and numbers):

- `slimy-0.1.0-dev+RANDOM-x86_64-windows` for Windows systems
- `slimy-0.1.0-dev+RANDOM-x86_64-linux-gnu` for most Linux systems
- `slimy-0.1.0-dev+RANDOM-x86_64-linux-musl` for Linux systems using musl libc - if you need this, you'll know

Once you have the right binary, open a terminal or command prompt in the folder containing it, and run the following command:

```
slimy-SYSTEM -- SEED RANGE THRESHOLD
```

- Replace `slimy-SYSTEM` with the binary name established earlier
- Replace `SEED` with your numeric world seed
- Replace `RANGE` with the number of chunks in each direction you want to search, centered at 0,0. 1000 is a good starting value
- Replace `THRESHOLD` with the minimum number of chunks you want in the despawn sphere. 40 is a good starting value

Once you run the command, it may take a few seconds to complete.
Just wait until it's finished and it'll output the results once it's done.

On systems with integrated GPUs, the CPU search may be faster than GPU search.
Some older GPUs may not be able to use the GPU search at all - this will be reported as an error in Vulkan initialization.
In these cases, you can use the CPU search mode like this:

```
slimy-SYSTEM -mcpu -- SEED RANGE THRESHOLD
```

[slimy-web]: https://silversquirl.github.io/slimy
[builds]: https://nightly.link/silversquirl/slimy/workflows/build/main/binaries.zip

## System requirements

- CPU search requires an x86_64 CPU and a few megabytes of RAM
- GPU search will work on most Vulkan-capable GPUs

## Advanced usage

For users wanting more control, there are some options that can change how Slimy behaves.

### Output format

By default, Slimy outputs results in a human-readable format.
However, if postprocessing of the results is desired, Slimy can be configured to output them in CSV format instead, using the `-f` option.

To output results to a CSV file named `results.csv`, use the following command:

```
slimy-SYSTEM -f csv -- SEED RANGE THRESHOLD >results.csv
```

### Unsorted output

If you want to see results more quickly, for example in order to allow further processing to happen in a streaming fashion, you can disable result sorting.
This is done with the `-u` option, as follows:

```
slimy-SYSTEM -u -- SEED RANGE THRESHOLD
```

This option can be combined with the `-f` option to produce unsorted output in a different format, eg. for an unsorted CSV file:

```
slimy-SYSTEM -uf csv -- SEED RANGE THRESHOLD >results.csv
```

### Custom thread count

By default, Slimy's CPU search will use as many CPU threads as are available.
If you wish to customize this behaviour, you can use the `-j` option.
For example, the following command will perform a CPU search with 4 threads:

```
slimy-SYSTEM -m cpu -j 4 -- SEED RANGE THRESHOLD
```

### JSON search parameters

Slimy allows specifying one or more search areas through JSON.
This can be done with a file, or by sending JSON directly to stdin, allowing generating parameters using a script.
To read JSON parameters from a file named `params.json`, use the following command:

```
slimy-SYSTEM -s params.json -- SEED
```

To read JSON parameters from stdin, use this command:

```
slimy-SYSTEM -s - -- SEED
```

The format of the JSON should be something like this:

```json
[{
	"threshold": THRESHOLD,
	"x0": X0,
	"z0": Z0,
	"x1": X1,
	"Z1": Z1
}, {
	"threshold": THRESHOLD_2,
	"x0": X0_2,
	"z0": Z0_2,
	"x1": X1_2,
	"Z1": Z1_2
}, ...]
```

### Benchmark mode

This mode is primarily meant for testing Slimy's performance during development.
However, it can also be useful for determining which search mode to use, or for a very rough estimate of system performance.

To run Slimy in benchmark mode, use the `-b` option with no other arguments:

```
slimy-SYSTEM -b
```
