## Personal profile



## Performance self-assessment

### business work



### technical work

1. 

2. 

3. 找问题过程：找了一些Europe的Scribe log放到staging上跑了一下，发现了一些导致报错的json的content-owner都包含一些西文字符，但这些原始的json在本地是能正常解码的，说明json格式没有问题，初步觉得是编码问题；java的StringEntity里面默认是iso-8859-1编码，即latin编码。SFX用的是utf-8编码，两者存在一些不兼容的情况（西文字符）。[对于特殊的unicode字符，SFX那边不做强转（为了以后不管换什么编码都没问题），默认还是按unicode编码返回，所以我们在接受到返回result的时候需要做一次转义]

   ```java
   root@redstats1-preprod.stickyadstv.com[PREPROD:10.235.2.48]:~/rpli$ java -cp "litestats.jar:./lib/*" tv.stickyads.tools.VServerMok
   SLF4J: Class path contains multiple SLF4J bindings.
   SLF4J: Found binding in [jar:file:/root/rpli/lib/slf4j-log4j12-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
   SLF4J: Found binding in [jar:file:/root/rpli/lib/slf4j-log4j12-1.7.21.jar!/org/slf4j/impl/StaticLoggerBinder.class]
   SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
   SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
   2020-10-23 02:40:47,974 INFO  [main] {"_id":{"d": {"$numberLong": "20201021"}, "b": 1793432673, "bri": 1, "z": 8906753},"value":{ "i" : 7 , "v1" : 7 , "v2" : 7 , "v3" : 8 , "v4" : 7 , "v5" : 8 , "c" : 1791372433 , "s" : 6364417 , "zt" : 4}},{"_id":{"d": {"$numberLong": "20201021"}, "b": 1787152305, "bri": 1, "z": 12069873, "ow": "zylo-Sexe, vengeance et séduction-4677500"},"value":{ "v3" : 1 , "v4" : 1 , "v5" : 1 , "c" : 1785016497 , "s" : 7849393 , "zt" : 3}},{"_id":{"d": {"$numberLong": "20201021"}, "b": 1793432657, "bri": 1, "z": 9166401},"value":{ "i" : 3 , "v1" : 3 , "v2" : 3 , "v3" : 3 , "v4" : 3 , "v5" : 1 , "c" : 1791372433 , "s" : 6574993 , "zt" : 2}}
   2020-10-23 02:40:48,169 INFO  [main] [{"_id":{"d":{"$numberLong":"20201021"},"b":1793432673,"bri":1,"z":8906753},"value":{"ct":84,"rv":84,"tf":0}},{"_id":{"d":{"$numberLong":"20201021"},"b":1787152305,"bri":1,"z":12069873,"ow":"zylo-Sexe, vengeance et séduction-4677500"},"value":{"ct":0,"rv":0,"tf":0}},{"_id":{"d":{"$numberLong":"20201021"},"b":1793432657,"bri":1,"z":9166401},"value":{"ct":36,"rv":36,"tf":0}}]
   ```

4.

4. 
5.  
6. 



## Development self-assessment



## Career aspiration

