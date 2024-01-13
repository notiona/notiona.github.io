---
title: troubleshooting - installing protobuf from source in mac arm64 (apple silicon)
date: 2024-01-14 00:32:30 +0900
categories: [TROUBLESHOOTING]
tags: [troubleshooting, protobuf, mac-arm64, arm64]
---

## Context

---
I was following the [streamlit contribution guide](https://github.com/streamlit/streamlit/wiki/Contributing), and one of their requirements when setting up your local development environment was to match the system version of [protobuf](https://protobuf.dev/) with the pip installed version.

You can check your system's protobuf version with:
```bash
protoc --version
```

Identifying that my system's protobuf version (`25.1`) was different from the pip installed version (`3.20.3`) was not difficult, but installing protobuf `3.20.3` was challenging as protobuf does not support osx-universal binary or osx-arm64 version of `3.20.3`.

So I decided to find a way to install protobuf from source, which is not a usual way to install things. 

I hope this can help whoever faces a similar issue in Apple Silicon machines.

## Environment

---
+ macOS Monterey 12.6.3
+ OSX arm64 (Apple Silicon)
+ zshell
+ This tutorial is effective as of January 2024

## Clone source repository

---
In the [desired protobuf release page](https://github.com/protocolbuffers/protobuf/releases/tag/v3.20.3) (mine is `3.20.3` in this case), find a source code `zip` or `tar.gz` file and download it.

After unzipping the file in local, `cd` (change directory) into the protobuf repository.

## Build from source

---

Install necessary build tools from homebrew.

```bash
brew install autoconf  
brew install automake  
brew install Libtool
```

Then run the following commands inside `protobuf` root directory to build and install from the source code.

```bash
autoreconf -i
./autogen.sh
./configure
make
sudo make install

export PATH=/opt/usr/local/bin:$PATH
```

With this, hopefully you would have installed the desired version of protobuf.

```bash
protoc --version
# should be libprotoc 3.20.3 now
```

## Korean Equivalent Post

---
`TBD`

## Reference

---
+ [Streamlit Contribution Guide](https://github.com/streamlit/streamlit/wiki/Contributing)
+ [Protobuf version 3.20.3 release page](https://github.com/protocolbuffers/protobuf/releases/tag/v3.20.3)
+ [install-protobuf-on-m1-mac](https://medium.com/@danny4410.eecs04/install-protobuf-on-m1-mac-852e4afa619f)

---

> Please feel free to point out any inaccurate or insufficient information. Also, please feel free to leave any questions or suggestions in the comments. 🙇‍♂️
{: .prompt-info}
