My idea for the project was to use the common crawl to look at housing data in the Netherlands. There are many different websites that have housing offers and crawling
gives an opportunity to look at all of them in a systematic manner

After inspecting the html structure of different websites, I found out that usually all the essential information (type of accomodation, price, city)
can be found in the title of the page. Because of that, my first attempt involved extracting information from titles only. This approach is probably quicker than going through 
the whole page, because it involves a dedicated Jsoup function .title(). It also filters out all sites which might mention something about rooms and prices, but which are not
offers themselves. 
