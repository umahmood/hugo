+++
date = "2016-06-11T14:43:49+01:00"
draft = false
title = "Updating Third Party Packages in Go"
hideToc = true
+++

Just a short post on how to update packages using _go get_.

To update all third party packages in your _GOPATH_ use the following command:

> go get -u all

To update a specific package, just provide the full package name to _go get_:

> go get -u github.com/gorilla/mux

What about vendor-ed packages? These are updated in exactly the same way as above:

> go get -u my-project/vendor/megacorp/foo

If you want more information about your _GOPATH_, run the command:

> go help gopath

Fin.
