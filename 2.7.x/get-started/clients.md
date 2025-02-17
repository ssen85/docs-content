---
# metadata # 
title:  Language Clients
description: Learn about our available language clients. 
date: 
# taxonomy #
tags: ["sdk", "sdks", "golang", "python","javascript","developers", "client", "python-pachyderm"]
series:
seriesPart:
directory: true 
weight: 4
---

`pachctl` is the command-line tool you use 
to interact with a {{%productName%}} cluster in your terminal. 
However,  external applications might need to
interact with {{%productName%}} directly through our APIs.

In this case, {{%productName%}} offers language specific SDKs in Go and Python.

{{% chunk go %}}
## Go Client

The {{%productName%}} team officially supports the Go client. It implements most of the functionalities provided with the `pachctl` CLI tool.

### Generate And Serve The godocs Locally

Golang's package (godoc), installed by default by the Go installer, can generate the Go client's documentation from the go code.

To generate the docs:

- Set your GOPATH: 

	```s
	export PATH=$(go env GOPATH)/bin:$PATH
	```

- In {{%productName%}}'s root directory, start the godocs server: 

	```s
	go run golang.org/x/tools/cmd/godoc -http=:6060 -goroot="<your go root directory - for example: /Users/yourusername/pachyderm>"
	```
				
	See https://pkg.go.dev/golang.org/x/tools/cmd/godoc for the complete list of flags available.

- In your favorite browser, run `localhost:6060/pkg/`


{{% notice warning %}}
 A compatible version of `gRPC` is needed when using the Go client.  You can identify the compatible version by searching for the version number next to `replace google.golang.org/grpc => google.golang.org/grpc` in https://github.com/pachyderm/pachyderm/blob/master/go.mod then:

```s
go get google.golang.org/grpc
cd $GOPATH/src/google.golang.org/grpc
git checkout v1.29.1
```
{{% /notice %}} 

### Running Go Examples

The {{%productName%}} godocs reference (see generation instructions above)
provides examples of how you can use the Go client API. You need to have a running {{%productName%}} cluster
to run these examples.

Make sure that you use your `pachd_address` in `client.NewFromAddress("<your-pachd-address>:30650")`.
For example, if you are testing on `minikube`, run
`minikube ip` to get this information.

See the [OpenCV Example in Go](https://github.com/pachyderm/pachyderm/tree/{{% majorMinorVersion %}}/examples/opencv) for more
information.
{{% /chunk %}}

## Python Clients

### Pachyderm-SDK (New)

The Python client `pachyderm-sdk` is the new Python client for {{%productName%}} and is officially supported by the {{%productName%}} team. 

- [Reference Docs](/sdk)
- [Client Initialization](/latest/sdk/install)
- [Starter Project](/latest/sdk/starter-project/)
<!-- - Tutorials (coming soon!) -->

### Python-Pachyderm (Old)

{{%notice warning%}}
The Python client `python-pachyderm` will be deprecated in 9 months as of 08/10/2023.
{{%/notice%}}

{{% notice note %}}
Use **python-pachyderm {{% pythonClientVersion %}}** with {{%productName%}} {{% majorMinorVersion %}}. 
{{% /notice %}}

You will find all you need to get you started or dive into the details of the available modules and functions in the [API documentation](https://python-pachyderm.readthedocs.io/en/stable/), namely:

- The [installation instructions](https://python-pachyderm.readthedocs.io/en/stable/getting_started.html#installation) and links to PyPI.
- A quick ["Hello World" example](https://python-pachyderm.readthedocs.io/en/stable/getting_started.html#hello-world-example) to jumpstart your understanding of the API.
- Links to python-pachyderm main Github repository with a [list of useful examples](https://github.com/pachyderm/python-pachyderm/tree/master/examples). 
- As well as the entire [**reference API**](https://python-pachyderm.readthedocs.io/en/stable/python_pachyderm.html).

## Node Client

Our Javascript client `node-pachyderm` has been deprecated.

## Other languages

{{%productName%}} uses a simple [protocol buffer API](https://github.com/pachyderm/pachyderm/blob/master/src/pfs/pfs.proto). Protobufs support [other languages](https://developers.google.com/protocol-buffers/), any of which can be used to programmatically use {{%productName%}}. We have not built clients for them yet. It is an easy way to contribute to {{%productName%}} if you are looking to get involved.
