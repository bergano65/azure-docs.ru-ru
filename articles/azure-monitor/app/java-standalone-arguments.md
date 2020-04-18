---
title: Мониторинг Java-приложений, работающих в любой среде - Azure Monitor Application Insight
description: Мониторинг производительности приложений для Java-приложений, работающих на любой среде с автономным агентом Java без инструментирования приложения. Распределенная карта отслеживания и применения.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641879"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Настройка автономного агента JVM args Java для azure Monitor Application Insight Insight



## <a name="azure-environments"></a>Среды Azure

Настройка [служб приложений](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Добавить JVM `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` arg `-jar <myapp.jar>`где-то раньше, например:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Args помещенные после `-jar <myapp.jar>` переданы к app как args программы.


## <a name="tomcat-8-linux"></a>Томктат 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Томктат установлен `apt-get` через или`yum`

Если вы установили Tomcat через `apt-get` или `yum`, `/etc/tomcat8/tomcat8.conf`то вы должны иметь файл .  Добавьте эту строку в конец этого файла:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat установлен через загрузку и распаковать

Если вы установили Tomcat через [https://tomcat.apache.org](https://tomcat.apache.org)скачать и распаковать от , то вы должны иметь файл `<tomcat>/bin/catalina.sh`.  Создайте новый файл в `<tomcat>/bin/setenv.sh` том же каталоге, названном со следующим содержанием:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Если файл `<tomcat>/bin/setenv.sh` уже существует, затем измените этот файл и добавьте `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` в `CATALINA_OPTS`.


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Запуск Томчата с командной строки

Найдите файл `<tomcat>/bin/catalina.bat`.  Создайте новый файл в `<tomcat>/bin/setenv.bat` том же каталоге, названном со следующим содержанием:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Котировки не являются необходимыми, но если вы хотите включить их, надлежащее размещение:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Если файл `<tomcat>/bin/setenv.bat` уже существует, просто измените этот файл и добавьте `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` в `CATALINA_OPTS`.

### <a name="running-tomcat-as-a-windows-service"></a>Запуск Tomcat как службы Windows

Найдите файл `<tomcat>/bin/tomcat8w.exe`.  Выполнить, что исполняемые `Java Options` и `Java` добавить `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` в под вкладку.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Отдельный сервер

Добавьте `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` к `JAVA_OPTS` существующей переменной среды в файле `JBOSS_HOME/bin/standalone.conf` (Linux) или `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Сервер домена

Добавить `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` к `jvm-options` `JBOSS_HOME/domain/configuration/host.xml`существующему в:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Если вы работаете несколько управляемых серверов на `applicationinsights.agent.id` одном `system-properties` хоста, вам нужно будет добавить к каждому: `server`

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

Указанное `applicationinsights.agent.id` значение должно быть уникальным. Он используется для создания субдиректоров в каталоге applicationinsights, так как каждый процесс JVM нуждается в своем собственном локальном confinsights config и локальном файле журнала applicationinsights. Кроме того, если отчет центральному коллектору `applicationinsights.properties` файл передается нескольким управляемым серверам, и поэтому указанный `applicationinsights.agent.id` данный необходим для переопределения `agent.id` настройки в этом общем файле. `applicationinsights.agent.rollup.id`может быть аналогичным образом указанва `system-properties` в сервере, если вам нужно переопределить настройку `agent.rollup.id` на управляемом сервере.


## <a name="jetty-9"></a>Джетти 9

Добавьте эти строки в`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Пайара 5

Добавить `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` к `jvm-options` `glassfish/domains/domain1/config/domain.xml`существующему в:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Open Management Console перейдите на **серверы > серверов приложений WebSphere > серверов приложений,** выберите соответствующие серверы приложений и нажмите на: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
В "Общие аргументы JVM" добавить следующее:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
После этого сохраните и перезапустите сервер приложения.


## <a name="openliberty-18"></a>OpenLiberty 18

Создайте новый файл `jvm.options` в каталоге `<openliberty>/usr/servers/defaultServer`сервера (например), и добавьте эту строку:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
