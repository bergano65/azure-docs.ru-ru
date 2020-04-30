---
title: Мониторинг приложений Java, выполняемых в любой среде — Azure Monitor Application Insights
description: Мониторинг производительности приложений Java, выполняющихся в любой среде с автономным агентом Java без инструментирования приложения. Распределенная трассировка и схема приложения.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641879"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Настройка изолированного агента Java ВИРТУАЛЬНОЙ машины Java args для Azure Monitor Application Insights



## <a name="azure-environments"></a>Среды Azure

Настройка [служб приложений](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Добавьте аргумент `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` виртуальной машины Java в место до `-jar <myapp.jar>`, например:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Аргументы, помещенные после `-jar <myapp.jar>` , передаются в приложение как аргументы программы.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat, установленный с помощью `apt-get` или`yum`

Если вы установили Tomcat `apt-get` через `yum`или, то у вас должен быть `/etc/tomcat8/tomcat8.conf`файл.  Добавьте следующую строку в конец этого файла:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat, установленные с помощью скачивания и распаковки

Если вы установили Tomcat с помощью Download и [https://tomcat.apache.org](https://tomcat.apache.org)unzip из, то у вас должен `<tomcat>/bin/catalina.sh`быть файл.  Создайте новый файл в том же каталоге с именем `<tomcat>/bin/setenv.sh` и следующим содержимым:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Если файл `<tomcat>/bin/setenv.sh` уже существует, измените его и добавьте `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` в. `CATALINA_OPTS`


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Запуск Tomcat из командной строки

Найдите файл `<tomcat>/bin/catalina.bat`.  Создайте новый файл в том же каталоге с именем `<tomcat>/bin/setenv.bat` и следующим содержимым:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Кавычки не являются обязательными, но если вы хотите включить их, следует учитывать следующее:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Если файл `<tomcat>/bin/setenv.bat` уже существует, просто измените его и добавьте `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` в. `CATALINA_OPTS`

### <a name="running-tomcat-as-a-windows-service"></a>Запуск Tomcat как службы Windows

Найдите файл `<tomcat>/bin/tomcat8w.exe`.  Запустите этот исполняемый файл `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` и добавьте `Java Options` его на `Java` вкладку.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Отдельный сервер

Добавьте `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` в существующую `JAVA_OPTS` переменную среды в файле `JBOSS_HOME/bin/standalone.conf` (Linux) или `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Сервер домена

Добавьте `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` к существующему `jvm-options` в `JBOSS_HOME/domain/configuration/host.xml`:

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

Если на одном узле выполняется несколько управляемых серверов, необходимо добавить `applicationinsights.agent.id` в для каждого из `system-properties` них: `server`

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

Указанное `applicationinsights.agent.id` значение должно быть уникальным. Он используется для создания вложенного каталога в каталоге applicationinsights, так как каждому процессу ВИРТУАЛЬНОЙ машины Java требуется собственная локальная applicationinsights config и локальный файл журнала applicationinsights. Кроме того, при отправке отчетов центральному сборщику `applicationinsights.properties` файл совместно используется несколькими управляемыми серверами, поэтому для переопределения `applicationinsights.agent.id` `agent.id` параметра в этом общем файле необходимо указать указанный параметр. `applicationinsights.agent.rollup.id`можно также указать на сервере, `system-properties` если необходимо переопределить `agent.rollup.id` параметр для каждого управляемого сервера.


## <a name="jetty-9"></a>Jetty 9

Добавьте эти строки в`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Пайара 5

Добавьте `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` к существующему `jvm-options` в `glassfish/domains/domain1/config/domain.xml`:

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

Откройте консоль управления, перейдите в раздел **серверы > WebSphere приложений серверы > серверы приложений**, выберите соответствующие серверы приложений и щелкните: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
В «Generic ВИРТУАЛЬНОЙ машины Java arguments» добавьте следующее:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
После этого сохраните и перезапустите сервер приложений.


## <a name="openliberty-18"></a>Опенлиберти 18

Создайте новый файл `jvm.options` в каталоге сервера (например `<openliberty>/usr/servers/defaultServer`,) и добавьте следующую строку:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
