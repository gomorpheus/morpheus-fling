# morpheus-fling

A small, command line based binary for aggregating useful statistics about large environments prior to deploying Morpheus.

## Functions

1. Port Scanning
2. OS Statistics
3. Elasticsearch Health
4. Elasticsearch Indices
5. Bundling and encrypting of output files and log file

### Port Scanning

`morpheus-fling` optionally reads ips from a file and associated ports and performs a scan of these ports to inspect openness.

### OS Statistics

`morpheus-fling` aggregates statistics about the OS it is installed on utilizing Linux kernel syscalls including memory, CPU, and available disk.

### Elasticsearch Health

`morpheus-fling` queries the localhost provided Elasticsearch REST API to get a health output.

### Elasticsearch Indices

`morpheus-fling` queries the localhost provided Elasticsearch REST API to get a breakdown of all indices and their respective health.

### Bundling

By default `morpheus-fling` will read your `current` log file for your running `morpheus-ui` service and place it as a json value in the
master file.  It encrypts the content and places a key into a .zip file with the output bundle.

## Usage

Download the binary directly to your server.

```bash
wget https://github.com/gomorpheus/morpheus-fling/releases/download/v2.1.1/morpheus-fling
```

Give posix permissions to be executed.  Binary allows the specification of an `-infile` for port scanning.  Binary will by default write output to a file called `output.txt` but user can flag a separate outfile to be created and appended to.

Run:

```bash
slimshady@morpheus1:~# ./morpheus-fling
```

Or run:

```bash
slimshady@morpheus1:~# ./morpheus-fling --infile=/path/to/foo.txt --outfile=/path/to/bar.txt
```

By default `morpheus-fling` only allows `1024` semaphores to be used based on standard ulimit expectations in environments.  This can also be adjusted by passing a `ulimit` flag.  To use an accurate representation from the system `morpheus-fling` is deployed to run `ulimit -n` on your system and qualify your binary run with this value flagged.
Example:

```bash
slimshady@morpheus1:~# ulimit -n
204800
slimshady@morpheus1:~# ./morpheus-fling --ulimit=204800
```

### Other Options

`morpheus-fling` also allows arguments to be pass for specific bundling of a logfile and specific naming of the archive.  Without specification the default is to look for `/var/log/morpheus-morpheus-ui/current` and bundle that with the outfile into `/tmp/bundler.zip`.

If you like you can do this, however

```bash
slimshady@morpheus1:~# ./morpheus-fling --logfile=/path/to/file --bundler=/path/to/archive_name.zip
```

### Inputs

As described, `morpheus-fling` allows an argument for a file of `ip:port` to be scanned for openness.  Format for the entries in this file should follow `ip:port` notation as below.

```text
10.30.21.100:10092
10.30.21.100:3306
10.30.21.193:22
10.30.21.100:15672
10.30.21.100:5672
```

### Report

`morpheus-fling` will generate a report during the run.  Below is an example of the contents.  The deafult is a file in the same directory as the binary called `output.txt` but this can be adjusted by making use of a flag during the run like `--outfile=/home/slimshady/foobar.txt`

```text
PORT SCANS:
[
 {
  "ip": "10.30.21.100",
  "port": 10092,
  "status": "closed"
 },
 {
  "ip": "10.30.21.100",
  "port": 3306,
  "status": "open"
 },
 {
  "ip": "10.30.21.193",
  "port": 22,
  "status": "closed"
 },
 {
  "ip": "10.30.21.100",
  "port": 15672,
  "status": "open"
 },
 {
  "ip": "10.30.21.100",
  "port": 5672,
  "status": "closed"
 }
]


OS STATS:
{
  "sysinfo": {
    "version": "0.9.2",
    "timestamp": "2019-07-09T16:12:52.316092459-06:00"
  },
  "node": {
    "hostname": "labs-den-demo-morpheus",
    "machineid": "2f67b055ae2d1078d70401de58a63a28",
    "timezone": "America/Denver"
  },
  "os": {
    "name": "Ubuntu 14.04.5 LTS",
    "vendor": "ubuntu",
    "version": "14.04",
    "release": "14.04.5",
    "architecture": "amd64"
  },
  "kernel": {
    "release": "4.2.0-42-generic",
    "version": "#49~14.04.1-Ubuntu SMP Wed Jun 29 20:22:11 UTC 2016",
    "architecture": "x86_64"
  },
  "product": {
    "name": "MBI-6219G-T-Pack",
    "vendor": "Supermicro",
    "version": "0123456789",
    "serial": "S215034X6B37474"
  },
  "board": {
    "name": "B2SS2-F",
    "vendor": "Supermicro",
    "version": "1.01",
    "serial": "ZD16BS000265",
    "assettag": "To be filled by O.E.M."
  },
  "chassis": {
    "type": 1,
    "vendor": "Supermicro",
    "version": "0123456789",
    "serial": "0123456789",
    "assettag": "To be filled by O.E.M."
  },
  "bios": {
    "vendor": "American Megatrends Inc.",
    "version": "1.0c",
    "date": "04/29/2016"
  },
  "cpu": {
    "vendor": "GenuineIntel",
    "model": "Intel(R) Xeon(R) CPU E3-1240 v5 @ 3.50GHz",
    "speed": 3500,
    "cache": 8192,
    "cpus": 1,
    "cores": 4,
    "threads": 8
  },
  "memory": {
    "type": "DDR4",
    "speed": 2400,
    "size": 65536
  },
  "storage": [
    {
      "name": "sda",
      "driver": "sd",
      "vendor": "ATA",
      "model": "SanDisk SD8SB8U2",
      "serial": "163047802208",
      "size": 256
    },
    {
      "name": "sdb",
      "driver": "sd",
      "vendor": "ATA",
      "model": "SanDisk SD8SB8U1",
      "serial": "164103801795",
      "size": 1024
    }
  ],
  "network": [
    {
      "name": "eth0",
      "driver": "igb",
      "macaddress": "0c:c4:7a:98:ba:8a",
      "port": "fibre",
      "speed": 1000
    },
    {
      "name": "eth1",
      "driver": "igb",
      "macaddress": "0c:c4:7a:98:ba:8b",
      "port": "fibre",
      "speed": 1000
    }
  ]
}
```
