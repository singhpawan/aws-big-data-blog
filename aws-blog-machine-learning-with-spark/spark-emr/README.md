# Large-Scale Machine Learning with Spark on Amazon EMR
This is the code repository for the code sample used in the AWS Big Data blog post Large-Scale Machine Learning with 
Spark on Amazon EMR.  It demonstrates an example machine learning workflow using Spark and MLlib on EMR.

## Prerequisites
  - Amazon Web Services account
  - [AWS Command Line Interface (CLI)]
  - [sbt](http://www.scala-sbt.org/)
  - [sbt-assembly](https://github.com/sbt/sbt-assembly)
  
## Building
```
sbt assembly
```

## Copying to S3
```
aws s3 cp spark-emr/target/scala-2.10/spark-emr-assembly-1.0.jar s3://your-bucket-name/$USER/spark/jars/spark-emr-assembly-1.0.jar
```

## Example invocation

```
aws emr create-cluster \
  --name "exampleJob" \
  --ec2-attributes KeyName=hadoop \
  --auto-terminate \
  --ami-version 3.8.0 \
  --instance-groups \
    InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge \
    InstanceGroupType=CORE,InstanceCount=1,InstanceType=r3.2xlarge \
  --log-uri s3://your-bucket-name/$USER/spark/`date +%Y%m%d%H%M%S`/logs \
  --applications Name=Spark,Args=[-x] \
 --bootstrap-actions Path=s3://support.elasticmapreduce/spark/install-spark \
 --steps "Name=\"Run Spark\",Type=Spark,Args=[--deploy-mode,cluster,--master,yarn-cluster,--conf,spark.executor.extraJavaOptions=-XX:MaxPermSize=256m,--conf,spark.driver.extraJavaOptions=-XX:MaxPermSize=512m,--class,ModelingWorkflow,--num-executors,14,--executor-memory,29g,--executor-cores,5,s3://your-bucket-name/$USER/spark/jars/spark-emr-assembly-1.0.jar,s3://support.elasticmapreduce/bigdatademo/intentmedia/,s3://your-bucket-name/$USER/spark/output/]"
```