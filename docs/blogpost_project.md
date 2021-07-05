# Big data final project 

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

First, it filters out records that are invalid or contain no header. I wanted to restrict my search to Dutch webistes only, so the next step is to check the URLs and keep only the ones with Dutch domain name: ".nl". Then, it looks at the webpage title and checks if it contains 3 regular expressions: one with price in euros, one with a Dutch city name (restricted to the cities where one can study Artificial Intelligence, due to personal interest), and one with some kind of accomodation: room, apartment, or studio. Then, if the title contains all these regular expressions, the program extracts the phrases matching these expressions and stores them in a DataFrame. This way, the data is stored in a distributed tabular representation, which enables easy analysis using SQL queries.


## Deployment on cluster


