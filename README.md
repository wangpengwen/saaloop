# Saaloop - A Scala DSL for Hadoop

## Introduction

[Saaloop](https://github.com/amir343/saaloop) is an experimental DSL implementation on top of [Apache Hadoop](http://hadoop.apache.org/) Java library. It avails from the strong Scala Type
System and provides you to write concise and easy-to-read DSL without any XML configuration

## How does it look like?

The classic word counts example looks like this in Saaloop:

```scala
import com.jayway.saaloop.dsl.Saaloop._
import org.apache.hadoop.io.{IntWritable, Text, LongWritable}
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat

object WordCount {

  def main(args: Array[String]) {

    val c = hadoopConfig {
      ("fs.temp.dir" << "/tmp") +
      ("dfs.name.dir" << "/tmp") +
      ("dfs.info.port" << "13400")
    }

    val m = mapper {
      (i: LongWritable, s: Text) => s.toString.split(" ").toList.map( w => (writable(w), writable(1)) )
    }

    val r = reducer {
      (key: Text, list: List[IntWritable]) => (key, writable(list.foldLeft(0)(_ + _.get())) )
    }

    job("myjob")(
      hadoopConfiguration = c,
      mapWith = m,
      reduceWith = r,
      inputFormatClass = classOf[TextInputFormat],
      outputFormatClass = classOf[TextOutputFormat[_,_]],
      waitForCompletion = true
    )
  }

}
```

Compare it to [this](http://wiki.apache.org/hadoop/WordCount)

## How to build

Simply clone the project and run `sbt` in the project root.