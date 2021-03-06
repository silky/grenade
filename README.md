Grenade
=======

```
First shalt thou take out the Holy Pin, then shalt thou count to three, no more, no less.
Three shall be the number thou shalt count, and the number of the counting shall be three.
Four shalt thou not count, neither count thou two, excepting that thou then proceed to three.
Five is right out.
```

💣 Machine learning which might blow up in your face 💣

Grenade is a dependently typed, practical, and pretty quick neural network library for concise and precise
specifications of complex networks in Haskell.

As an example, a network which can achieve less than 1.5% error on mnist can be specified and
initialised with random weights in under 10 lines of code with
```haskell
randomMnistNet :: MonadRandom m => m (Network Identity '[('D2 28 28), ('D3 24 24 10), ('D3 12 12 10), ('D3 12 12 10), ('D3 8 8 16), ('D3 4 4 16), ('D1 256), ('D1 256), ('D1 80), ('D1 80), ('D1 10), ('D1 10)])
randomMnistNet = do
  a :: Convolution 1 10 5 5 1 1  <- randomConvolution
  let b :: Pooling 2 2 2 2        = Pooling
  c :: Convolution 10 16 5 5 1 1 <- randomConvolution
  let d :: Pooling 2 2 2 2        = Pooling
  e :: FullyConnected 256 80     <- randomFullyConnected
  f :: FullyConnected 80  10     <- randomFullyConnected
  return $ a :~> b :~> Relu :~> c :~> d :~> FlattenLayer :~> Relu :~> e :~> Logit :~> f :~> O Logit
```

The network can be thought of as a heterogeneous list of layers, and its type signature includes a type
level list of the shapes of the data passed between the layers of the network.

In the above example, the input layer can be seen to be a two dimensional (`D2`) image with 28 by 28 pixels.
The last item in the list is one dimensional (`D1`) with 10 values, representing the categories of the mnist data.

Layers in Grenade are represented as Haskell classes, so creating one's own is easy in downstream code. If the shapes
of a network are not specified correctly and a layer can not sensibly perform the operation between two shapes, then
it will result in a compile time error.

Thanks
------
Writing a library like this has been on my mind for a while now, but a big shout out must go to Justin Le, whose
dependently typed fully connected network inspired me to get cracking, gave many ideas for the type level tools I
needed, and was a great starting point for writing this library.

Performance
-----------
Grenade is backed by hmatrix and blas, and uses a pretty clever convolution trick popularised by Caffe, which
is surprisingly effective and fast. So for many small scale problems it should be sufficient.

That said, it's currently stuck on a single core and doesn't hit up the GPU, so there's a fair bit of performance
sitting there begging.

Training 15 generations over Kaggle's mnist training data took a few hours.

Contributing
------------
Contributions are welcome.
