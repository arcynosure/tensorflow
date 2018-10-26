# TensorFlow in Go

Construct and execute TensorFlow graphs in Go.

[![GoDoc](https://godoc.org/github.com/tensorflow/tensorflow/tensorflow/go?status.svg)](https://godoc.org/github.com/tensorflow/tensorflow/tensorflow/go)

> *WARNING*: The API defined in this package is not stable and can change
> without notice. The same goes for the awkward package path
> (`github.com/tensorflow/tensorflow/tensorflow/go`).

## Installing TensorFlow for Go

### TensorFlow C library

Install the TensorFlow C library which is required for the TensorFlow Go package.

### Download

Download and install the TensorFlow Go package and its dependencies:

```
go get github.com/tensorflow/tensorflow/tensorflow/go
```

And validate your installation:

```
go test github.com/tensorflow/tensorflow/tensorflow/go
```

## Build

### Example program

With the TensorFlow Go package installed, create an example program with the following source code (hello_tf.go):

```
package main

import (
    tf "github.com/tensorflow/tensorflow/tensorflow/go"
    "github.com/tensorflow/tensorflow/tensorflow/go/op"
    "fmt"
)

func main() {
    // Construct a graph with an operation that produces a string constant.
    s := op.NewScope()
    c := op.Const(s, "Hello from TensorFlow version " + tf.Version())
    graph, err := s.Finalize()
    if err != nil {
        panic(err)
    }

    // Execute the graph in a session.
    sess, err := tf.NewSession(graph, nil)
    if err != nil {
        panic(err)
    }
    output, err := sess.Run(nil, []tf.Output{c}, nil)
    if err != nil {
        panic(err)
    }
    fmt.Println(output[0].Value())
}
```

## Run

Run the example program:

```
go run hello_tf.go
```
The command outputs: Hello from TensorFlow version number
The program may generate the following warning messages, which you can ignore:

```
W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library
wasn't compiled to use *Type* instructions, but these are available on your
machine and could speed up CPU computations.
```

## Building the TensorFlow C library from source

If the "Quickstart" instructions above do not work (perhaps the release archives
are not available for your operating system or architecture, or you're using a
different version of CUDA/cuDNN), then the TensorFlow C library must be built
from source.

### Prerequisites

-   [bazel](https://www.bazel.build/versions/master/docs/install.html)
-   Environment to build TensorFlow from source code
    ([Linux](https://www.tensorflow.org/install/install_sources#PrepareLinux)
    or [OS
    X](https://www.tensorflow.org/install/install_sources#PrepareMac)).
    If you don't need GPU support, then try the following: `sh # Linux sudo
    apt-get install python swig python-numpy # OS X with homebrew brew install
    swig`

### Build


1.  Build the TensorFlow C library:

    ```sh
    cd ${GOPATH}/src/github.com/tensorflow/tensorflow
    ./configure
    bazel build --config opt //tensorflow:libtensorflow.so
    ```

    This can take a while (tens of minutes, more if also building for GPU).

2.  Make `libtensorflow.so` available to the linker. This can be done by either:

    a. Copying it to a system location, e.g.,

    ```sh
    sudo cp ${GOPATH}/src/github.com/tensorflow/tensorflow/bazel-bin/tensorflow/libtensorflow.so /usr/local/lib
    ```

    OR

    b. Setting environment variables:

    ```sh
    export LIBRARY_PATH=${GOPATH}/src/github.com/tensorflow/tensorflow/bazel-bin/tensorflow
    # Linux
    export LD_LIBRARY_PATH=${GOPATH}/src/github.com/tensorflow/tensorflow/bazel-bin/tensorflow
    # OS X
    export DYLD_LIBRARY_PATH=${GOPATH}/src/github.com/tensorflow/tensorflow/bazel-bin/tensorflow
    ```

3.  Build and test:

    ```sh
    go test github.com/tensorflow/tensorflow/tensorflow/go
    ```

### Generate wrapper functions for ops

Go functions corresponding to TensorFlow operations are generated in `op/wrappers.go`. To regenerate them:

Prerequisites:
- [Protocol buffer compiler (protoc) 3.x](https://github.com/google/protobuf/releases/)
- The TensorFlow repository under GOPATH

```sh
go generate github.com/tensorflow/tensorflow/tensorflow/go/op
```

## Support

Use [stackoverflow](http://stackoverflow.com/questions/tagged/tensorflow) and/or
[Github issues](https://github.com/tensorflow/tensorflow/issues).

## Contributions

Contributions are welcome. If making any signification changes, probably best to
discuss on a [Github issue](https://github.com/tensorflow/tensorflow/issues)
before investing too much time. Github pull requests are used for contributions.
