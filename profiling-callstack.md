# Execution and wait time visualization

> WORK IN PROGRESS

Your functions take time to execute (as seen on flame-graphs) but they also spawn asynchronous requests and pass a callback to regain control of the program flow.

Here's a tool to visually understand how time is spent in your app.

[dprof](https://github.com/AndreasMadsen/dprof) - not much to add here, just follow the readme.

**What's the difference between dprof and flame-graphs?**

In flame-graphs, time is the value represented on the graph, dprof also visualizes the relationships between functions and their callers.
