## Linux系统下怎样配置多个Tomcat同时运行呢，首先修改变量为第一个tomcat，然后修改第二个tomcat启动的脚本  
1、修改环境变量  
***vi /etc/profile*** 
```
####### 工程1 tomcat1#######
export CATALINA_BASE=/data/server/tomcat
export CATALINA_HOME=$CATALINA_BASE
export TOMCAT_HOME=$CATALINA_BASE

#######  工程2  tomcat2#######
export CATALINA_2_BASE=/data/tomcat-upgrade
export CATALINA_2_HOME=$CATALINA_2_BASE
export TOMCAT_2_HOME=$CATALINA_2_BASE


#######  工程3  tomcat3#######
export CATALINA_3_BASE=/data/tomcat-ifengSearch
export CATALINA_3_HOME=$CATALINA_3_BASE
export TOMCAT_3_HOME=$CATALINA_3_BASE


########  JAVA  jdk  环境 #########
export JAVA_HOME=/data/jdk1.7
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
``` 
***source /etc/profile***   

2、修改 对应tomcat中的 catalina.sh，添加环境变量  

***vi /data/tomcat-upgrade/bin/catalina.sh*** 
```
# 修改 和 /etc/profile 中的 变量相同
export CATALINA_BASE=$CATALINA_2_BASE
export CATALINA_HOME=$CATALINA_2_HOME
``` 
3、 修改 tomcat的配置文件 server.xml  
修改下面标记的端口即可，防止端口占用    
***vi /data/tomcat-upgrade/conf/server.xml***   
```
 <Connector port="7080" protocol="HTTP/1.1"
               connectionTimeout="30000"
               maxHttpHeaderSize="20480"
               minSpareThreads="20"
               maxSpareThreads="50"
               maxThreads="500"
               acceptCount="500"
               maxProcessors="1000"
               minProcessors="5"
               useURIValidationHack="false"
               enableLookups="false"
               redirectPort="8443"/>
    


    <!-- Define an AJP 1.3 Connector on port 8009 -->
<Connector port="7088" protocol="AJP/1.3" redirectPort="8443" />
```
