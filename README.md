#OptNet - reducing memory usage in torch neural networks

Memory optimizations for torch neural networks.
Heavily inspired from the `Optimizer` from https://github.com/facebook/fb-caffe-exts

## How does it work ?

It goes over the network and verify which buffers can be reused (currently only
the `output` of each module).

## Visualizing the memory reuse

We can analyse the sharing of the internal buffers by looking at the computation
graph of the network before and after the sharing.

For that, we have the `createGraph(net, input, opts)` function, which creates the
graph corresponding to the network `net`. The generated graph contains the storage
id of each `output`, and same colors means same storage.

Let's have a look:

```lua
models = require 'optnet.models'
modelname = 'googlenet'
net, input = models[modelname]()

generateGraph = require 'optnet.graphgen'

g = generateGraph(net, input)

graph.dot(g,modelname,modelname)

```

This generates the following graph:

Now what happens after we optimize the network ?

```lua
models = require 'optnet.models'
modelname = 'googlenet'
net, input = models[modelname]()

generateGraph = require 'optnet.graphgen'

optnet = require 'optnet'

optnet.optimizeMemory(net, input)

g = generateGraph(net, input)

graph.dot(g,modelname..'_optimized',modelname..'_optimized')
```