# Getting started with Logstash 2.4
A tutorial how I got Logstash 2.4 on Windows 7 up and running based on the [Elastic tutorial - Getting Stated with Logstash][1].

## Download 
- Download [logstash 2.4.0][6]. I used the zip version and unpacked it to `C:\Users\foo\logstash-2.4.0`
- Lateron in this tutorial you need [elasticsearch 2.4.1][7] where the logstash output will be stored.

## Smoke test
The first step was to check if logstash was working. This was quite easy, as stated in the tutorial: 

    cd C:\Users\foo\logstash-2.4.0
	bin/logstash -e 'input { stdin {  } } output { stdout {  } }'

This did not work in the first instance. My fault was, that I tryed to start logstash in a [git bash][2]. And there are some things missing, so I got an error message like 

    io/console not supported; tty will not be manipulated
	Bundler::GemNotFound: Could not find jruby-openssl-0.9.16-java in any of the sources ...

Switching to the real MS-DOS console did the trick and the command worked. 

	bin\logstash.bat -e 'input { stdin {  } } output { stdout {  } }'

I could enter some text, which was printed in the logstash format in the MS-DOS console. 


## My first pipe file
Mastered the smoketest my next step was to create a pipe file: pipe.conf. So I created the file and tested it
with the command: 

	bin\logstash.bat -f pipe.conf

Here is the content of the file **pipe.conf**:
	
	# The # character at the beginning of a line indicates a comment. Use
	# comments to describe your configuration.
	input {
		stdin {  }
	}
	output {
		stdout {  }
	}

This worked as expexted. 

## Parse real log data...
I downloaded the sample data set: [logstash-tutorial-dataset][3] and extracted it to my logstash 2.4 folder.
The pipe file must be configured to read the sample data. This was done with the **file input** configuration. Important to know: 

 - `path` information must be an absolute path

With the following **pipe.conf** the sample data is read and print out on the MS-DOS console screen

	input {
		# stdin {  }
		file {
			# must be an absulut path
			path => "C:\Users\foo\logstash-2.4.0\logstash-tutorial-dataset"
			start_position => beginning 
			ignore_older => 0 
		}
	}
	output {
		stdout {  }
	}

Now I could test the pipe file with the know command

	bin\logstash.bat -f pipe.conf

As result I got the log messages printed to the MS-DOS console:

	2016-10-11T09:12:37.943Z FOO1128 83.149.9.216 - - [04/Jan/2015:05:13:53 +0000] "GET /favicon.ico HTTP/1.1" 200 3638 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
	 2016-10-11T09:12:37.943Z FOO1128 24.236.252.67 - - [04/Jan/2015:05:14:10 +0000 ] "GET /favicon.ico HTTP/1.1" 200 3638 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_ 64; rv:26.0) Gecko/20100101 Firefox/26.0"
	 ...

That was fine. Rerunning the command did not work. After some time I figured out, this has
to do with `.sincedb*` file. With removing the `.sincedb*` file I was able to rerun the example with the desired output.

### Excursus .sincedb
The `.sincedb*` file "keeps tracks of the current position in each file" [\[cite-1\]][4]. So if you rerun the
example there will be no output. You have to remove the `.sincedb*` file, before the rerun. By default the file is
stored in your home directory. But you can specify the location in the pipe file in the file input section

	# It must be an absolute file path
    sincedb_path => "C:\Users\foo\logstash-2.4.0\.sincedb_path-logstash-tutorial-dataset"


## ...and store it in a file
To store the logstash output in a file, the output section has to be extended with an file section. As you can
have several output formats in logstash I appended it after the `stdout {  }`. With this I can see in the MS-DOS
console if the pipe worked or not.

Heres the updated **pipe.conf**: 

	input {
		file {
			# must be an absulut path
			path => "C:\Users\foo\logstash-2.4.0\logstash-tutorial-dataset"
			start_position => beginning 
			ignore_older => 0 
			sincedb_path => "C:\Users\foo\logstash-2.4.0\.sincedb_path-logstash-tutorial-dataset"
		}
	}
	output {
		stdout {  }
		file { 
			path => "C:\Users\foo\logstash-2.4.0\logstash-tutorial-output.log"
		}
	}

I removed my sincedb file `C:\Users\foo\logstash-2.4.0\.sincedb_path-logstash-tutorial-dataset` and could rerun
the sample data with: 

	bin\logstash.bat -f pipe.conf

Now the logstash message was stored in `C:\Users\foo\logstash-2.4.0\logstash-tutorial-output.log`. When I opened
the file it looked like: 

    {"message":"83.149.9.216 - - [04/Jan/2015:05:13:42 +0000] \"GET /presentations/logstash-monitorama-2013/images/kibana-search.png HTTP/1.1\" 200 203023 \"http://semicomplete.com/presentations/logstash-monitorama-2013/\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36\"","@version":"1","@timestamp":"2016-10-11T09:12:37.907Z","path":"C:\\Users\\foo\\logstash-2.4.0\\logstash-tutorial-dataset","host":"FOO1128"}

## Let's filter
Following the tutorial I tried the `grok` and `geoip` filter in the pipe file. In the first step I tried `grok`
which enhance the log line with information about: **clientip, ident, auth, timestamp, verb, request, httpversion,
response, bytes, referrer** and **agent**. 

This was a little bit confusing as I understood that the log information would be transformed only to these
information bits and not that the log line was enhance by this information. Anyway the filter works somehow. 

Heres the updated **pipe.conf** with the `grok` filter: 

	input {
		file {
			# must be an absulut path
			path => "C:\Users\foo\logstash-2.4.0\logstash-tutorial-dataset"
			start_position => beginning 
			ignore_older => 0 
			sincedb_path => "C:\Users\foo\logstash-2.4.0\.sincedb_path-logstash-tutorial-dataset"
		}
	}
	filter {
		grok {
			match => { "message" => "%{COMBINEDAPACHELOG}"}
		}
	}
	output {
		stdout {  }
		file { 
			path => "C:\Users\foo\logstash-2.4.0\logstash-tutorial-output.log"
		}
	}

Which generated this output:

    {"message":"83.149.9.216 - - [04/Jan/2015:05:13:42 +0000] \"GET /presentations/logstash-monitorama-2013/images/kibana-search.png HTTP/1.1\" 200 203023 \"http://semicomplete.com/presentations/logstash-monitorama-2013/\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36\"","@version":"1","@timestamp":"2016-10-13T15:35:59.611Z","path":"C:\\Users\\foo\\logstash-2.4.0\\logstash-tutorial-dataset","host":"FOO1128","clientip":"83.149.9.216","ident":"-","auth":"-","timestamp":"04/Jan/2015:05:13:42 +0000","verb":"GET","request":"/presentations/logstash-monitorama-2013/images/kibana-search.png","httpversion":"1.1","response":"200","bytes":"203023","referrer":"\"http://semicomplete.com/presentations/logstash-monitorama-2013/\"","agent":"\"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36\""}

The same goes for the `geoip` filter, it enhanced the log lines with the information about: **geoip: { ip, country_code2, country_code3, country_name, continent_code, region_name, city_name, latitude, longitude, timezone, real_region_name** and **location }**.

Heres the updated **pipe.conf** with the `geoip` filter: 

	input {
		file {
			# must be an absulut path
			path => "C:\Users\foo\logstash-2.4.0\logstash-tutorial-dataset"
			start_position => beginning 
			ignore_older => 0 
			sincedb_path => "C:\Users\foo\logstash-2.4.0\.sincedb_path-logstash-tutorial-dataset"
		}
	}
	filter {
		grok {
			match => { "message" => "%{COMBINEDAPACHELOG}"}
		}
		geoip {
			source => "clientip"
		}
	}
	output {
		stdout {  }
		file { 
			path => "C:\Users\foo\logstash-2.4.0\logstash-tutorial-output.log"
		}
	}

Which resulted in the following logline:

    {"message":"83.149.9.216 - - [04/Jan/2015:05:13:42 +0000] \"GET /presentations/logstash-monitorama-2013/images/kibana-search.png HTTP/1.1\" 200 203023 \"http://semicomplete.com/presentations/logstash-monitorama-2013/\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36\"","@version":"1","@timestamp":"2016-10-13T15:41:48.229Z","path":"C:\\Users\\foo\\logstash-2.4.0\\logstash-tutorial-dataset","host":"FOO1128","clientip":"83.149.9.216","ident":"-","auth":"-","timestamp":"04/Jan/2015:05:13:42 +0000","verb":"GET","request":"/presentations/logstash-monitorama-2013/images/kibana-search.png","httpversion":"1.1","response":"200","bytes":"203023","referrer":"\"http://semicomplete.com/presentations/logstash-monitorama-2013/\"","agent":"\"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36\"","geoip":{"ip":"83.149.9.216","country_code2":"RU","country_code3":"RUS","country_name":"Russian Federation","continent_code":"EU","region_name":"48","city_name":"Moscow","latitude":55.75219999999999,"longitude":37.6156,"timezone":"Europe/Moscow","real_region_name":"Moscow City","location":[37.6156,55.75219999999999]}}

## Change output and store in elasticsearch
So the filters works somehow. The next step was storing the data in elasticsearch. Obvious that you have to
download, install and run elasticsearch for that. How is described in the [elasticsearch 2.4 documentation][5]. 

Done that I had an elasticsearch instance running on http://localhost:9200.

Following the logstash tutorial I replaced the output section and ended up in this pipe configuration: 

	input {
		file {
			# must be an absulut path
			path => "C:\Users\foo\logstash-2.4.0\logstash-tutorial-dataset"
			start_position => beginning 
			ignore_older => 0 
			sincedb_path => "C:\Users\foo\logstash-2.4.0\.sincedb_path-logstash-tutorial-dataset"
		}
	}
	filter {
		grok {
			match => { "message" => "%{COMBINEDAPACHELOG}"}
		}
		geoip {
			source => "clientip"
		}
	}
	output {
		stdout {  }
		elasticsearch {
			hosts => [ "localhost:9200" ]
		}
	}

Which a short configtest: `bin\logstash.bat -f pipe.conf --configtest` confirmed with **Configuration OK**.

And finally getting **Butter bei die Fisch** ( German saying, should be in english something near to *get down to brass tacks* ). Be sure to removed the `.sincedb` file before!

Again I run the logstash command: 

	bin\logstash.bat -f pipe.conf

And could see that something happend in the elasticsearch shell. To figure out in which index elasticsearch stored
the output I had a quick look in my browser under: 

	http://localhost:9200/_cat/indices?v

Which lists all indices. There I could figure out that it should be `logstash-2016.10.15`. 

	health status index               pri rep docs.count docs.deleted store.size pri.store.size 
	yellow open   l4                    5   1       1000            0    461.5kb        461.5kb 
	yellow open   .kibana               1   1          1            0      3.1kb          3.1kb 
	yellow open   logstash-2016.10.15   5   1        100            0    241.8kb        241.8kb

Armed with this information I could check now if the import worked, by calling the following URL in my browser: 

	http://localhost:9200/logstash-2016.10.15/_search?pretty&q=response=200

As response I got a lot of Json with the data of the sample data set.

----

cite-1: https://www.elastic.co/guide/en/logstash/current/plugins-inputs-file.html

Created: 2016-10-15


[1]: https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html
[2]: https://git-for-windows.github.io/
[3]: https://download.elastic.co/demos/logstash/gettingstarted/logstash-tutorial.log.gz
[4]: https://www.elastic.co/guide/en/logstash/current/plugins-inputs-file.html
[5]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html
[6]: https://www.elastic.co/downloads/logstash
[7]: https://www.elastic.co/downloads/elasticsearch
