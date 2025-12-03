+++
title = "Merkle Trees and Data Verification"
date = 2020-06-23T19:01:24+01:00
draft = false
hideToc = true
+++

A Merkle tree is a binary tree where the nodes stores hashes of data, rather than the data itself. The leaf nodes store hashes of the data and the parent nodes are the results of concatenating the left and right children and then applying a hash function on the result.

To read more about Merkle trees go [here](https://en.wikipedia.org/wiki/Merkle_tree).

In this post, I will talk about how data verification is performed in Merkle trees.

Firstly, a client receives a root hash from a trusted server/source:

```
root_hash = "1234"
```

The client then asks peers on an untrusted peer-2-peer network, to send us the data chunks associated with the root hash, we receive the chunks:

```
Data Chunks: [1] [2] [3] [4]
```

The data chunks came from random people on the network and we cannot trust them. So we build a Merkle tree from the received data chunks:

```
   H(1234)
  /       \
 H12     H34
 / \     / \
H1 H2   H3 H4
```

We then compare the root hash of the tree we just built H(1234), to the root hash we received from our trusted server. And in this instance, they match which means that the data has not been corrupted or tampered with.

#### Audit Proof

In the example above we had to download all the chunks before we could verify the data. It would be better if we could verify chunks as they are downloaded from peers, and not wait for all the data to arrive. This is possible and it is known as partial verification or audit-proof.

Our trusted server has the following Merkle tree:

```
   H(1234)
  /       \
 H12     H34
 / \     / \
H1 H2   H3 H4
```

The client downloads the following data chunk from the untrusted network:

```
Data Chunks: [3]
```

Whilst the client is downloading other chunks, we want to verify this chunk. So we perform the following steps:

1. The client sends data chunk 3 to the trusted server.
2. The server checks that Hash(3) is in the tree. it is.
3. The server sends back hashes **H(4)** and **H(12)**. We need these to compute the root hash.
4. The client then computes:

```
H(34)   = H(3)  + H(4)
H(1234) = H(12) + H(34)
```

And we compare H(1234) to _root_hash_ and they match. We did not need all the data chunks to get the answer.

What is also amazing about this process is that the trusted server had to only send a little information. If the number of data chunks were doubled, the server would only have to send two more hashes and the client would only have to perform two more hash calculations.

The Merkle tree is a really interesting data structure and is used in a wide range of applications, including Bitcoin, Git, and BitTorrent.

I hope you found this post helpful.

Fin.
