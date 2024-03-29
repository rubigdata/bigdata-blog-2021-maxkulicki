I managed to run the Zeppelin notebooks successfully on my laptop, I also went through the official Spark documentation, and I think that I now have a decent understanding of how Spark works:
* **Lazy evaluation**\
In Spark, the transformations that we apply to RDDs are evaluated in a lazy way - when we define the transformation, Spark keeps track of it but does not immediately give the result of it, unless we specifically ask for it with a command like .take() or .takeSample() . This way, we can construct complex transformations from simple pieces, and execute them in one go, without having to execute piece by piece. By separating design from execution like this, we can better manage the workflow and save resources - both time and memory

* **Caching RDDs**\
Another tool that helps the time and memory efficiency is the explicit memory management using caching. By default, the data that you process is not saved anywhere, it just flows through as you operate on it. If you know you will need to access a specific dataset multiple times - for example in some kind of loop or interactive setting, you can store it in memory using .cache() function. This way, the data persists and doesn't have to be loaded every time it's used. 

* **Partitioning** \
To efficiently process data on a cluster, it needs to be distributed among the nodes in a process called partitioning. When we define a file to be loaded, we decide into how many partitions it will be divided: "val rdd= sc.textFile (“file.txt”, 5)" (in this case, 5). When we know something about the data and the processing we perform, we can use that to partition in a smart way. Every partition is stored in exactly one node, so the number of partitions should be at least equal to the number of cores available, otherwise we will just have idle nodes. It shouldn't be much more than that, because then extra time is spent on scheduling the tasks for different partitions.
We can also repartition our data after we process it, which might be useful if, for example, we filtered it and it became much smaller.

These are the basic aspects of efficient use of RDDs in Spark, there is a lot more detail to it but I think it's a good starting point to make some informed decisions.
