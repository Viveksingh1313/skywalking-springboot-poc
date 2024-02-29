# Skywalking poc
## Requirement
* JDK 11 (As of now this application works on Java 11.)
* Gradle 7+(Tested in 7.4.1)
* Docker(with docker-compose)

## Run oap server, elastic-search, skywalking-ui modules in  docker
./docker-compose docker-compose -f skywalking.yml up

## Run services
```bash
gradle :zull-gateway:bootRun
gradle :user-service:bootRun
```

## Test with postman
import `skywalking-poc.postman_collection.json` into postman
Hit these curl calls multiple times:  

curl --location 'http://localhost:9080/demo/api/user' \
--header 'token: test' \
--header 'Content-Type: application/json' \
--data '{
"name": "test320"
}'

curl --location 'http://localhost:9080/demo/api/user' \
--header 'token: test'

## Visualize data Skywalking UI : localhost:8080


### Additional info about project :
## Download java agent
* http://skywalking.apache.org/downloads/
    ```bash
    tar xzf apache-skywalking-apm-6.2.0.tar.gz 
    mv -f apache-skywalking-apm-bin/agent ./agent
    rm -rf apache-skywalking-apm-bin
    ```
* Add manually trace in code
	* gradle
	```gradle
 	implementation 'org.apache.skywalking:apm-toolkit-trace:9.1.0'	```
	* Code: `@Trace`, `@TraceCrossThread`, `ActiveSpan`, `TraceContext`
* Add logback trace id
	* gradle
	```gradle
	implementation 'org.apache.skywalking:apm-toolkit-logback-1.x:9.1.0'
	```
	* logback.xml
	```logback
	<conversionRule conversionWord="tid" converterClass="org.apache.skywalking.apm.toolkit.log.logback.v1.x.LogbackPatternConverter"/>
    <property name="CONSOLE_LOG_PATTERN"
              value="%clr([%tid]%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}"/>

## Enable agent
For gradle as an example
```gradle
bootRun {
	jvmArgs = ["-Dskywalking.agent.service_name=zull-gateway", "-javaagent:$projectDir/skywalking-agent/skywalking-agent.jar"]
}
```
* `skywalking.agent.service_name` should be same as service name in `application.yml` to avoid some issues.

