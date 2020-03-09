# elk-on-docker
  filebeat/logstash/elasticsearch/kibana
## logback config
  layout pattern config
```
  <property name="LOG_MSG"
                value="%d{yyyy-MM-dd HH:mm:ss.SSS} %level %thread %logger{50} %method %L traceId:%X{logId} - %msg%n"/>
```
  rolling config
```
  <appender name="INVOKE_LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--日志文件路径，日志文件名称-->
        <file>${LOG_HOME}/${LOG_PREFIX}/${LOG_PREFIX}-invoke.log</file>
        <!-- 设置滚动策略，当天的日志大小超过 ${MAX_FILE_SIZE} 文件大小时候，新的内容写入新的文件， 默认10MB -->
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <!--日志文件路径，新的 ALL 日志文件名称，“ i ” 是个变量 -->
            <fileNamePattern>${LOG_DIR}/${LOG_PREFIX}-invoke-%d{HH}.%i.log</fileNamePattern>
            <maxFileSize>${MAX_FILE_SIZE}</maxFileSize>
            <maxHistory>${MAX_HISTORY}</maxHistory>
            <totalSizeCap>${totalSizeCap}</totalSizeCap>
            <cleanHistoryOnStart>${cleanHistoryOnStart}</cleanHistoryOnStart>
        </rollingPolicy>

        <!-- 输出的日志内容格式化-->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>${LOG_MSG}</pattern>
        </layout>
    </appender>
```
## setup elk stack monitoring in kibana

 http://127.0.0.1:5601/app/monitoring

## es config
  set jvm heap config for the min es required
  [official document for es jvm detail](https://www.elastic.co/guide/en/elasticsearch/reference/current/heap-size.html)
  ```
  -Xms2g 
  -Xmx2g 
  ```
  dump path default $PWD/data  (/usr/share/elasticsearch/data)
  gc log path default $PWD/logs (/usr/share/elasticsearch/log)
  
  max_file_descriptors config is importance for es running,[check the config is not greater than 65535](http://localhost:9200/_nodes/stats/process?filter_path=**.max_file_descriptors)

  [config vm max map count](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html)
## set .security index for elastic stack security
  ```
  docker-compose up -d es01
  ```
  wait es setup and begin setting password for base users
  ```
  bin/elasticsearch-setup-passwords interactive
  ```
  my all defaut password is "1357qetu"
## security logstash
  setup es and kibana
  ```
  docker-compose --compatibility -f docker-compose.yml up -d es01 es02 kibana
  ```
## add user logstash_writer for logstash output elasticsearch
  [add role and user for logstash_writer](https://www.elastic.co/guide/en/logstash/current/ls-security.html)
## run stack
  ```
  docker-compose --compatibility -f docker-compose.yml up -d es01 es02 kibana logstash
  ```
  filebeat is avaliable is this compose,but normally deploy on the other cluster


