# Big data project - Maksymilian Kulicki (s1020170)

## Idea
My idea for the project was to use the common crawl to look at housing data in the Netherlands. I have been struggling a lot with finding a place for myself in the Netherlands, so I thought tackling the problem with the help of Big Data might be very useful. There are many different websites that have housing offers and crawling gives an opportunity to look at all of them in a systematic manner. The goal is to identify these webistes and extract the relevant infromation - type of accomodation, location, and price.

I inspected the html structure of different housing websites and found out that usually all the essential information can be found in the title of the page. Because of that, my first attempt involved extracting information from titles only. This approach is probably quicker than going through the whole page, because it involves a dedicated Jsoup function .title(). It also filters out all sites which might mention something about rooms and prices, but which do not contain offers themselves. 

## Code development
After identifying my goal, I started the development by making a small prototype crawl with relevant data. I used the code given in the notebook and the URL: ""https://kamernet.nl/huren/kamers-nijmegen"" as a starting point of the crawl. This way, I obtained 259 warc records, some of which contained housing offers. 

After that, I wrote the code to identify the relevant websites and extract information:

```scala

val price_regex = "€[0-9]+".r
val city_regex = "(Nijmegen|Amsterdam|Utrecht|Groningen|Maastricht)".r
val type_regex = "((K|k)amer|(R|r)oom)|(A|a)ppartement|(S|s)tudio".r
val site_regex = "(\\w)*.nl".r

val data = warcs.
               map{ wr => wr._2}.
               filter{ _.isValid() }.
               map{ wr => wr.getRecord()}.
               filter{ wr => wr.getHeader().getUrl() != null}.
               filter{ wr => site_regex.findFirstIn(wr.getHeader().getUrl()).isDefined}.
               map{ wr => wr.getHttpStringBody()}.
               map{ wb => Jsoup.parse(wb).title()}.
               filter{
                        title => price_regex.findFirstIn(title).isDefined
                    }.
               filter{
                        title => city_regex.findFirstIn(title).isDefined
                    }.
                filter{
                        title => type_regex.findFirstIn(title).isDefined
                    }.
                map{
                        title => (price_regex.findFirstIn(title).get.substring(1), 
                        city_regex.findFirstIn(title).get,
                        type_regex.findFirstIn(title).get
                        )                    
                    }.toDF("price", "city", "acc")
               
data.show

```

First, it filters out records that are invalid or contain no header. I wanted to restrict my search to Dutch webistes only, so the next step is to check the URLs and keep only the ones with Dutch domain name: ".nl". Then, it looks at the webpage title and checks if it contains 3 regular expressions: one with price in euros, one with a Dutch city name (restricted to the cities where one can study Artificial Intelligence, due to personal interest), and one with some kind of accomodation: room, apartment, or studio. Then, if the title contains all these regular expressions, the program extracts the phrases matching these expressions and stores them in a DataFrame. This way, the data is stored in a distributed tabular representation, which enables easy analysis using SQL queries, like this one here, which is a result of running the code on the Zeppelin notebook over the small Kamernet crawl: 

![image](https://user-images.githubusercontent.com/49609518/124577744-d4dc7d80-de4d-11eb-8dbd-26a5e6bcb28e.png)

## Deployment on cluster and final remarks

After seeing that the code indeed does the job in the notebook, it was time to try to deploy it on the cluster. First, I converted it into a standalone app and tried running it in my local container, over the Kamernet crawl. I managed to do it by simply following the steps in the tutorial - putting it inside a main function, shipping the function to the proper location in the container, and assembling it using sbt. The results were the same as in the notebook trial.

Then, I ran that app on the Redbad cluster. First, I only used one randomly selected warc file. It complied and ran successfully, but the file contained no websites matching my criteria. so it returned no results. Then, I tried running it over one of the many folders full of warc files, again, with no results. In a desperate attempt, I decided to try once more, using another randomly chosen folder. This one actually returned some results:

![image](https://user-images.githubusercontent.com/49609518/124617031-e4bc8780-de76-11eb-96e0-207ba7a0f359.png)

This proves that the program works, and gives us some limited information - Utrecht and Groningen likley contain more offers than other cities, and apartments are usually more expensive than rooms. Unfortunately, this is not enough data to perform any sophisticated analysis. It took 2.5 hours to execute so I decided not to run my program over the full crawl, due to the infeasible runtime and the fact that the cluster was still very busy with other people's jobs. 

I did manage to prove that my code extracts data from relevant websites, and that I am able to execute it on a proper cluster. I just wasn't able to run it over the whole crawl, which probably would contain some more Dutch housing offers. If I was able to gather more results, I would perform an analysis with SQL queries, regarding the number of offers and the average, min and max prices of different accomodation types in different cities. 


