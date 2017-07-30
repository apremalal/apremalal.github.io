---
title: Converting a JSON stream to CSV format using python
date: 2013-06-02 19:55:16
categories: JSON
---

Most of the times it is very useful to have look at the big picture of the data  before digging deep in to the details.Often times we find data in JSON format. For example lets say we want to perform some analysis on twitter streamed data,someone might find CSV formatted twitter data helpful in their analysis. This might also be helpful for people who are competing in Kaggle competitions where most of the time they provide data in JSON streamed format. Here is a very simple python script which will do the job.

```
import unicodecsv,json
import sys

def convert2csv(data):
  fieldnames = ['user_id','name','average_stars','review_count','type'] #Filed names to filter and record, json stream

	with open('test.csv', 'wb+') as f:			
		    dict_writer = unicodecsv.DictWriter(f, fieldnames=fieldnames)
		    dict_writer.writerow(dict(zip(fieldnames, fieldnames)))
		    for line in data:
				row = json.loads(line)				
				dict_writer.writerow(row)

if __name__ == '__main__':
	inputdata = open(sys.argv[1])
	convert2csv(inputdata)
```

JSON encoding may vary sometime. Below I have provided few resources which might help in fixing encoding issues.
