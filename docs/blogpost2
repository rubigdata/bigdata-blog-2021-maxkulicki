In my setup, I have a laptop with Windows 10 on it and Ubuntu terminal installed with WSL 2. 
At first I tried running the code from the Hadoop tutorial. I ran into problems when I tried running the map reduce job with yarn. I had to apply the fix from the FAQ and
run the job without yarn, seemingly because my disk is not suited for this kind of tasks. This helped and I finished the tutorial.

Then, after setting up the hdfs and testing it, I ran the example WordCount program. To get the java code to the docker, I put it in my Ubuntu home folder and copied it using
this command: "docker cp WordCount.java hello-hadoop:/opt/hadoop/WordCount.java". Then I ran the map reduce and copied the results from the docker to my local files: 
"docker cp hello-hadoop:/opt/hadoop/output output". This allowed me to inspect the output and determine whether Romeo or Juliet appears more often. I searched through the
file using Ctrl+F, looking for "Romeo" and "Juliet" in different forms, accompanied by various puctuation marks. It turns out Romeo is mentioned 313 times and Juliet only 206 times.

After that worked, I modified the WordCount.java code so that it only finds the words in which "Romeo" or "Juliet" appears and counts them for me. It required
changing the line: 
```
  context.write(word, one);
```  
 to: 
```
if(word.toString().contains("Romeo") || word.toString().contains("ROMEO"))
	context.write(new Text("Romeo"), one);
if(word.toString().contains("Juliet") || word.toString().contains("JULIET"))
	context.write(new Text("Juliet"), one);
```          

It worked and the output was the same as my counting results. This means I was able to answer this question making only one pass over the corpus:

![screenshot of terminal](https://github.com/rubigdata/hello-hadoop-2021-maxkulicki/blob/master/r_and_j.png)

To count all words, you would have to modify the same part of code, but this time put there:
```
context.write(new Text("words in total"), one);
```
This way all words would be assigned the same key and would all be counted as one value.

To count all the characters, you would have to for each word output the number of characters, not just 'one'. This can be done using the Text.getLength() function.


          
          
