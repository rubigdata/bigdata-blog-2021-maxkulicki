I managed to run the Zeppelin notebooks successfully on my laptop, and I think that I now have a decent understanding of how Spark works:
* lazy evaluation
In Spark, the transformations that we apply to RDDs are evaluated in a lazy way - when we define the transformation, Spark keeps track of it but does not immediately give the result of it, unless we specifically ask for it with a command like .take() or .takeSample() . This way, we can construct complex transformations from simple pieces, and execute them in one go, without having to execute piece by piece. By separating design from execution like this, we can better manage the workflow and save resources - both time and memoy
* caching RDDs


* partitioning of the RDDs
* query optimizer
