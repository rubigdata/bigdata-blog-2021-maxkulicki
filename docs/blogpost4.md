This assignment went into more detail on how to use Spark, specifically the Dataset API, for data analysis. Datasets are a more structured representation of data than RDDs, they contain named columns like a relational database. This allows us to operate on a higher level of abstraction and to use tools like SQL queries or data visualisation. They form a bridge between the physical structure of the data being distributed over many machines, and logical structure, containing information about the types of data stored and relationships between them.

The first notebook was concerned mainly with data cleaning and preparation. It showed me that even an official municipal dataset is not clean enough to analyze it directly. A very clear example was that some artworks were labelled as being created in the year 9999, which, I suppose, means that the year is not known. However, some other data points had a null creation date, which means that the labels for unknown year were just not consistent. This is a problem that is inherent to the data itself, not an artifact of our processing, and unfortunately can only be solved by very careful inspection of the data. To clean it, the most straightforward option is to discard the data that does not meet our requirements. In this case, it was only a small fraction of the whole dataset so it was not a huge issue.

The second notebook explored a second dataset, with all buildings in Nijmegen and their addresses. To be able to join the two datasets, it was necessary to transform the coordinates from the Dutch to the global system. It could be done with a custom function, which after we defined it, could be applied to the data. The analysis was not complete, so I tried to finish it myself. Here is my the query that resulted in all districts with no artworks in the database:

![brave_sUauB6re02](https://user-images.githubusercontent.com/49609518/115579669-adeac280-a2c6-11eb-940f-217cead25cbe.png)

Then, I looked at the artworks that were not successfully matched with the address database, their location and year of creation. It seems that both the year and location are spread around, without any clear pattern. It might be that they just don't precisely match any address because they are in some weird spots.

![brave_NzJzyinS52](https://user-images.githubusercontent.com/49609518/115581994-cf4cae00-a2c8-11eb-9cef-df61592502a1.png)

This assignment gave a good overview of the Dataset API, as well as the process of cleaning, querying and visualising data using Spark, which are essential skills for any kind of data analysis.
