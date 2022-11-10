# 求最低气温的代码：

`[jian@HJ-hadoop ~]$ mkdir my_weather`

`[jian@HJ-hadoop ~]$ cd my_weather`

`[jian@HJ-hadoop my_weather]$ mkdir input`

`[jian@HJ-hadoop my_weather]$ cd input`

将数据集上传到/input目录；Java代码上传到/my_weather目录

`[jian@HJ-hadoop input]$ pwd`

`/home/jian/my_weather/input`

`[jian@HJ-hadoop input]$ cd ..`

`[jian@HJ-hadoop my_weather]$ ls`

`input  WeatherJob.java`

`[jian@HJ-hadoop my_weather]$ mkdir weather_classes`

`[jian@HJ-hadoop my_weather]$ cd weather_classes/`

`[jian@HJ-hadoop weather_classes]$ ll`

`total 0`

`[jian@HJ-hadoop weather_classes]$ cd ..`

`[jian@HJ-hadoop my_weather]$ export HADOOP_CLASSPATH=$(hadoop classpath)`

`[jian@HJ-hadoop my_weather]$ echo $HADOOP_CLASSPATH`

`/home/jian/hadoop-3.2.4/etc/hadoop:/home/jian/hadoop-3.2.4/share/hadoop/common/lib/*:/home/jian/hadoop-3.2.4/share/hadoop/common/*:/home/jian/hadoop-3.2.4/share/hadoop/hdfs:/home/jian/hadoop-3.2.4/share/hadoop/hdfs/lib/*:/home/jian/hadoop-3.2.4/share/hadoop/hdfs/*:/home/jian/hadoop-3.2.4/share/hadoop/mapreduce/lib/*:/home/jian/hadoop-3.2.4/share/hadoop/mapreduce/*:/home/jian/hadoop-3.2.4/share/hadoop/yarn:/home/jian/hadoop-3.2.4/share/hadoop/yarn/lib/*:/home/jian/hadoop-3.2.4/share/hadoop/yarn/*`

`[jian@HJ-hadoop my_weather]$ javac -classpath ${HADOOP_CLASSPATH} -d weather_classes WeatherJob.java`

`[jian@HJ-hadoop my_weather]$ cd weather_classes/`

`[jian@HJ-hadoop weather_classes]$ ll`

`total 12`

`-rw-rw-r-- 1 jian jian 2104 Nov  8 06:50 WeatherJob.class`

`-rw-rw-r-- 1 jian jian 1885 Nov  8 06:50 WeatherJob$WeatherMapper.class`

`-rw-rw-r-- 1 jian jian 2050 Nov  8 06:50 WeatherJob$WeatherReducer.class`

`[jian@HJ-hadoop weather_classes]$ cd ..`

`[jian@HJ-hadoop my_weather]$ jar -cvf WeatherJob.jar -C weather_classes/ .`

`added manifest`

`adding: WeatherJob$WeatherMapper.class(in = 1885) (out= 780)(deflated 58%)`

`adding: WeatherJob$WeatherReducer.class(in = 2050) (out= 898)(deflated 56%)`

`adding: WeatherJob.class(in = 2104) (out= 1049)(deflated 50%)`

`[jian@HJ-hadoop my_weather]$ ll`

`total 12`

`drwxrwxr-x 2 jian jian  30 Nov  8 06:47 input`

`drwxrwxr-x 2 jian jian  107 Nov  8 06:50 weather_classes`

`-rw-rw-r-- 1 jian jian 3495 Nov  8 06:54 WeatherJob.jar`

`-rw-rw-r-- 1 jian jian 4388 Nov  8 06:45 WeatherJob.java`

`[jian@HJ-hadoop my_weather]$ start-all.sh`

`[jian@HJ-hadoop my_weather]$ hdfs  dfs  -mkdir  /my_weather`

`2022-11-08 06:58:03,249 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable`

`[jian@HJ-hadoop my_weather]$ hdfs  dfs  -mkdir  /my_weather/input`

`2022-11-08 06:58:30,034 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable`

`[jian@HJ-hadoop my_weather]$ cd input/`

`[jian@HJ-hadoop input]$ ll`

`total 80`

`-rw-rw-r-- 1 jian jian 79570 Nov  8 06:47 weather_data.txt`

`[jian@HJ-hadoop input]$ cd ..`

`[jian@HJ-hadoop my_weather]$ ll`

`total 12`

`drwxrwxr-x 2 jian jian  30 Nov  8 06:47 input`

`drwxrwxr-x 2 jian jian  107 Nov  8 06:50 weather_classes`

`-rw-rw-r-- 1 jian jian 3495 Nov  8 06:54 WeatherJob.jar`

`-rw-rw-r-- 1 jian jian 4388 Nov  8 06:45 WeatherJob.java`

`[jian@HJ-hadoop input]$ hdfs dfs -put weather_data.txt /my_weather/input`

`2022-11-08 07:16:07,795 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable`

`[jian@HJ-hadoop my_weather]$ hadoop jar WeatherJob.jar WeatherJob /my_weather/input /my_weather/output`

 `completed successfully`

`[jian@HJ-hadoop my_weather]$ hdfs dfs -cat /my_weather/output/*                2022-11-08 07:24:13,734 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable`

`MIN   -7.9`

`[jian@HJ-hadoop my_weather]$ hdfs dfs -get /my_weather/output                 2022-11-08 07:26:24,346 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable`

# 结果：

MIN   -7.9