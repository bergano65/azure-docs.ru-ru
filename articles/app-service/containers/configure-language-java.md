---
title: Настройка приложения Linux на Java — служба приложений Azure | Документация Майкрософт
description: Сведения о настройке приложений Java, работающих в службе приложений Azure в Linux.
keywords: Служба приложений Azure, веб-приложение, linux, oss, java, java ee, jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 91368ac3b1d7948257fa9e55debc862567593425
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341387"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Настройка приложения Linux Java для службы приложений Azure

Служба приложений Azure в Linux позволяет разработчикам быстро создавать, развертывать и масштабировать веб-приложения на основе пакетов Tomcat Java или Java Standard Edition (SE) в полностью управляемой службе под управлением Linux. Возможно развертывание приложений с подключаемыми модулями Maven из командной строки или в редакторах, например Visual Studio Code, Eclipse или IntelliJ.

Это руководство содержит основные понятия и инструкции для разработчиков Java, которые используют встроенный контейнер Linux в службе приложений. Если вы раньше не использовали службы приложений Azure, выполните [быстрого запуска Java](quickstart-java.md) и [Java с помощью руководства PostgreSQL](tutorial-java-enterprise-postgresql-app.md) первого.

## <a name="deploying-your-app"></a>Развертывание приложения

Можно использовать [подключаемого модуля Maven для службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) для развертывания WAR-файлы и .jar файлов. Развертывание с помощью популярных интегрированных сред разработки также поддерживается с [набора средств Azure для IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) или [средств Azure для Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

В противном случае метод развертывания будет зависеть от вашей тип архива:

- Чтобы развернуть файлы WAR в Tomcat, используйте конечную точку `/api/wardeploy/` для публикации файла архива. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Чтобы развернуть файлы JAR в образах Java SE, используйте конечную точку `/api/zipdeploy/` сайта Kudu. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Не развертывайте файлы WAR или JAR с помощью FTP. Средство FTP предназначено для передачи сценариев запуска, зависимостей или других файлов среды выполнения. Оно не является оптимальным решением для развертывания веб-приложений.

## <a name="logging-and-debugging-apps"></a>Ведение журнала и отладка приложений

Отчеты о производительности, визуализация трафика и проверка работоспособности доступны на портале Azure для каждого приложения. Дополнительные сведения см. в разделе [Общие сведения о диагностике службы приложений Azure](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Доступ к консоли SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Дополнительные сведения см. в разделе о [потоковой передаче журналов с помощью Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Ведение журнала приложений

Включите [ведение журнала приложений](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) с помощью портала Azure или [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config), чтобы настроить службу приложений для записи выходных данных стандартной консоли приложения и потоков ошибок стандартной консоли в локальную файловую систему или хранилище BLOB-объектов Azure. Запись журналов в локальную файловую систему экземпляра службы приложений отключается через 12 часов после настройки ведения журнала. Если необходимо более длительное хранение, настройте приложение для записи выходных данных в контейнер больших двоичных объектов. Журналы приложений Java и Tomcat можно найти в `/home/LogFiles/Application/` каталога.

Если приложение использует [Logback](https://logback.qos.ch/) или [Log4j](https://logging.apache.org/log4j) для трассировки, то эти данные трассировки можно передать в Azure Application Insights для просмотра, выполнив инструкции по настройке платформы ведения журнала в разделе [Просмотр журналов трассировки Java в Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Средства устранения неполадок

На основе встроенных образов Java [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) операционной системы. Используйте `apk` диспетчер пакетов для установки Устранение неполадок средства или команды.

### <a name="flight-recorder"></a>«Черный ящик»

Все образы Linux на Java в службе приложений имеют Zulu «черный ящик» установлен, поэтому можно легко подключиться к виртуальной машине Java и запустить профилировщик записи или создания дампа кучи.

#### <a name="timed-recording"></a>Истекло время ожидания записи

Для получения работы SSH в службе приложений и запуска `jcmd` команду, чтобы просмотреть список всех процессов Java, запущенных. В дополнение к jcmd сам вы должны увидеть приложения Java, запущенного с идентификатором процесса (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Выполните следующую команду, чтобы начать запись 30-секундной виртуальной машины Java. Это профиля виртуальной машины Java и создайте файл JFR `jfr_example.jfr` в домашнем каталоге. (Замените 116 pid ваше приложение Java).

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Во время 30-секундный интервал, можно проверить записи выполняется путем запуска `jcmd 116 JFR.check`. При этом будут отображены все записи для указанного процесса Java.

#### <a name="continuous-recording"></a>Непрерывная записи

Можно использовать «черный ящик» Zulu постоянно профилирование приложения Java с минимальным влиянием на производительность во время выполнения ([источника](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Чтобы сделать это, выполните следующую команду Azure CLI, чтобы создать параметр приложения с именем JAVA_OPTS с необходимой конфигурации. Содержимое параметра приложения, JAVA_OPTS передаются `java` команды при запуске приложения.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Дополнительные сведения см. в разделе [Справочник по командам Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Анализ записей

Используйте [FTPS](../deploy-ftp.md) можно скачать файл JFR на локальный компьютер. Чтобы проанализировать файл JFR, загрузите и установите [Zulu пакет управления](https://www.azul.com/products/zulu-mission-control/). Инструкции по Zulu пакет управления, см. в разделе [документации Azul](https://docs.azul.com/zmc/) и [инструкции по установке](https://docs.microsoft.com/en-us/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Настройка

Служба приложений Azure для Linux поддерживает поле настройки и настройки с помощью портала Azure и интерфейса командной строки. Ознакомьтесь со следующими статьями для конфигурацию Java конкретного веб-приложения:

- [Настройка параметров приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Настройка личного домена](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Включение SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Добавление CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Настройка на сайт Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Настройка параметров среды выполнения Java

Чтобы задать объем выделенной памяти и других параметров среды выполнения виртуальной машины Java в средах Java SE и Tomcat, создайте [параметр приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) с именем `JAVA_OPTS` с параметрами. При запуске служба приложений для Linux передает этот параметр в качестве переменной среды в среду выполнения Java.

На портале Azure в разделе **Параметры приложения** для веб-приложения создайте параметр приложения `JAVA_OPTS`, включающий в себя дополнительные параметры, такие как `-Xms512m -Xmx1204m`.

Чтобы настроить параметр приложения из подключаемого модуля Maven, добавьте значение параметра/тегов в разделе подключаемый модуль Azure. В следующем примере задается конкретных минимальный и максимальный размер кучи Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Разработчики, запускающие отдельное приложение в одном слоте развертывания в плане службы приложений, могут использовать следующие параметры.

- Экземпляры B1 и S1: `-Xms1024m -Xmx1024m`
- Экземпляры В2 и S2: `-Xms3072m -Xmx3072m`
- Экземпляры B3 и S3: `-Xms6144m -Xmx6144m`

При настройке параметров кучи приложения просмотрите сведения о плане службы приложений и примите во внимание, что наличие нескольких приложений и одного слота развертывания требует оптимального выделения памяти.

Если вы развертываете приложение JAR-ФАЙЛ, его имя должно `app.jar` таким образом, чтобы встроенного изображения мог корректно идентифицировать приложение. (Подключаемый модуль Maven делает этот переименование автоматически.) Если вы не хотите переименовать JAR-ФАЙЛУ для `app.jar`, вы можете отправить сценарий оболочки с помощью команды для запуска JAR-ФАЙЛУ. Вставьте полный путь для этого сценария в [файл запуска](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#startup-file) текстовое поле в разделе конфигурации портала.

### <a name="turn-on-web-sockets"></a>Включение веб-сокетов

Включите для приложения поддержку веб-сокетов на портале Azure в разделе **Параметры приложения**. Необходимо будет перезапустить приложение, чтобы этот параметр вступил в силу.

Включите поддержку веб-сокетов с помощью Azure CLI, выполнив следующую команду.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Затем перезапустите приложение.

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Настройка кодировки по умолчанию

На портале Azure в разделе **Параметры приложения** для веб-приложения создайте параметр приложения `JAVA_OPTS` со значением `-Dfile.encoding=UTF-8`.

Можно также настроить параметр приложения с помощью подключаемого модуля Maven для службы приложений. Добавьте теги имени и значения параметра в конфигурацию подключаемого модуля.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Отрегулировать время ожидания запуска

Если приложение Java, особенно велик, следует увеличить предельное время запуска. Чтобы сделать это, создайте параметр приложения, `WEBSITES_CONTAINER_START_TIME_LIMIT` и присвойте ему значение количество секунд ожидания до истечения времени ожидания службы приложений. Максимальное значение равно `1800` секунд.

## <a name="secure-applications"></a>Защита приложений

Для приложений Java, работающих в службе приложений для Linux, предлагается тот же набор [рекомендаций по обеспечению безопасности](/azure/security/security-paas-applications-using-app-services), что и для других приложений. 

### <a name="authenticate-users"></a>Проверка подлинности пользователей

Настроить проверку подлинности приложений на портале Azure с помощью **проверки подлинности и авторизации** параметр. Вы можете включить аутентификацию с помощью Azure Active Directory или имен для входа в социальные сети, таких как Facebook, Google или GitHub. На портале Azure можно настроить только один поставщик аутентификации. Дополнительные сведения приведены в разделе [Настройка приложения службы приложений для использования входа с помощью Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) и связанных статьях о других поставщиках удостоверений. Если необходимо включить несколько поставщиков входа, следуйте инструкциям в статье [Настройка проверки подлинности и авторизации в службе приложений Azure](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

#### <a name="tomcat"></a>Tomcat

Tomcat приложение может получить доступ пользователя утверждения непосредственно из сервлет Tomcat путем приведения основной объект объект карты. Объект-сопоставление каждого типа утверждения, сопоставляется коллекцию утверждений для этого типа. В следующем коде `request` является экземпляром класса `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Теперь вы можете проверить `Map` объект для любого конкретного утверждения. Например в следующем фрагменте кода выполняется итерация по все типы утверждений и печатает содержимое каждой коллекции.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Чтобы завершить сеансы пользователей и выполнения других действий, см. документацию на [проверку подлинности службы приложений и авторизации использования](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Имеется также Официальная документация на Tomcat [HttpServletRequest интерфейс](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) и его методы. Следующие сервлетов, которые также являются структура данных методов в зависимости от конфигурации службы приложений:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Чтобы отключить эту функцию, создайте параметр приложения с именем `WEBSITE_AUTH_SKIP_PRINCIPAL` со значением `1`. Чтобы отключить все фильтры сервлетов, добавленные службы приложений, создайте параметр с именем `WEBSITE_SKIP_FILTERS` со значением `1`.

#### <a name="spring-boot"></a>Spring Boot

Разработчики для Spring Boot могут использовать [краткое руководство по использованию Spring Boot и Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable), чтобы защитить приложения с помощью привычных заметок и интерфейсов API Spring Security. Увеличьте максимальный размер заголовка в файле `application.properties`. Мы рекомендуем использовать значение `16384`.

### <a name="configure-tlsssl"></a>Настройка TLS/SSL

Следуйте инструкциям в разделе [Руководство. Привязывание существующего настраиваемого SSL-сертификата к веб-приложениям Azure](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), чтобы передать существующий SSL-сертификат и привязать его к доменному имени приложения. По умолчанию приложение по-прежнему будет разрешать HTTP-подключения. Выполните соответствующие инструкции в этом руководстве, чтобы принудительно включить SSL и TLS.

### <a name="use-keyvault-references"></a>Использование ссылок на хранилище ключей

[Хранилище ключей Azure](../../key-vault/key-vault-overview.md) обеспечивает централизованное управление секретами с журналом политики и аудита доступа. Можно хранить секретные данные (например, пароли или строки подключения) в хранилище ключей и использовать эти секреты в приложении посредством переменных среды.

Во-первых, следуйте инструкциям по [предоставления вашему приложению доступ к Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) и [делает ссылку на секрет хранилища ключей в параметр приложения](../app-service-key-vault-references.md#reference-syntax). Вы можете проверить, что ссылка разрешается секрет с печатью переменную среды при удаленном входе терминалов службы приложений.

Чтобы внедрить эти секреты в файле конфигурации Spring или Tomcat, используйте синтаксис переменной путем внедрения кода среды (`${MY_ENV_VAR}`). Файлы конфигурации Spring, см в этой документации по [выразили конфигураций](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Настройка платформ APM

В этом разделе показано, как подключить приложения Java, развернутых в службе приложений Azure на платформе Linux с помощью NewRelic и AppDynamics наблюдения за производительностью приложений (APM) платформ.

[Настройка New Relic](#configure-new-relic)
[Настройка AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Настройка New Relic

1. Создайте учетную запись NewRelic на сайте [NewRelic.com](https://newrelic.com/signup)
2. Скачайте агент Java с NewRelic. Его имя файла будет аналогично `newrelic-java-x.x.x.zip`.
3. Скопируйте ключ лицензии. Он понадобиться позже для настройки агента.
4. [SSH в экземпляр службы приложений](app-service-linux-ssh-support.md) и создайте новый каталог `/home/site/wwwroot/apm`.
5. Отправьте распакованные файлы агента Java NewRelic в каталог `/home/site/wwwroot/apm`. Файлы для агента должны быть в каталоге `/home/site/wwwroot/apm/newrelic`.
6. Измените файл YAML в каталоге `/home/site/wwwroot/apm/newrelic/newrelic.yml` и замените значение лицензии в заполнителе собственным ключом лицензии.
7. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если приложение использует **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Если вы используете **WildFly**, см. в документации New Relic [здесь](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) руководство по установке агента Java и JBoss конфигурации.
    - Если у вас уже есть переменная среды `JAVA_OPTS` или `CATALINA_OPTS`, добавьте параметр `javaagent` в конец текущего значения.

### <a name="configure-appdynamics"></a>Настройка AppDynamics

1. Создайте учетную запись AppDynamics на сайте [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Скачайте агент Java с веб-сайта AppDynamics. Имя файла будет аналогично `AppServerAgent-x.x.x.xxxxx.zip`.
1. [SSH в экземпляр службы приложений](app-service-linux-ssh-support.md) и создайте новый каталог `/home/site/wwwroot/apm`.
1. Отправьте файлы агента Java в каталог `/home/site/wwwroot/apm`. Файлы для агента должны быть в каталоге `/home/site/wwwroot/apm/appdynamics`.
1. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если вы используете **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.
    - Если вы используете **WildFly**, см. в документации AppDynamics [здесь](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) руководство по установке агента Java и JBoss конфигурации.
    
## <a name="configure-jar-applications"></a>Настройка приложений JAR-ФАЙЛ

### <a name="starting-jar-apps"></a>Запуск JAR-ФАЙЛ приложения

По умолчанию служба приложений ожидает, что приложение JAR-ФАЙЛ должен называться `app.jar`. Если у него есть это имя, оно будет запущено автоматически. Для пользователей, Maven, можно задать имя JAR-файла, включая `<finalName>app</finalName>` в `<build>` часть вашей `pom.xml`. [То же самое можно сделать в Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) , задав `archiveFileName` свойство.

Если вы хотите использовать другое имя для JAR-ФАЙЛУ, необходимо также указать [команду запуска](app-service-linux-faq.md#built-in-images) , выполняющийся на JAR-файл. Например, `java -jar my-jar-app.jar`. Можно задать значение для вашей команды запуска на портале в разделе конфигурации > Общие параметры, или с помощью параметра приложения с именем `STARTUP_COMMAND`.

### <a name="server-port"></a>Порт сервера

Службы приложений Linux направляет входящие запросы на порт 80, поэтому приложения должен прослушивать порт 80. Это можно сделать в конфигурации приложения (например Spring `application.properties` файл), или в команду запуска (например, `java -jar spring-app.jar --server.port=80`). См. следующую документацию для распространенных платформ Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Источники данных

### <a name="tomcat"></a>Tomcat

Эти инструкции применимы ко всем подключениям к базе данных. Необходимо будет заменить значения заполнителей на имя класса драйвера и JAR-файл выбранной базы данных. Ниже приведена таблица с именами классов и ссылками для скачивания драйверов для распространенных баз данных.

| База данных   | Имя класса драйвера                             | Драйвер JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Загрузить](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Скачать](https://dev.mysql.com/downloads/connector/j/) (выберите "Platform Independent" (Независимо от платформы)) |
| SQL Server; | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Загрузить](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Чтобы настроить Tomcat для использования Java Database Connectivity (JDBC) или API сохраняемости Java (JPA), сначала нужно настроить `CATALINA_OPTS` переменной среды, считываемое Tomcat при запуске. Задайте эти значения с помощью параметра приложения в [подключаемом модуле Maven для службы приложений](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Или задать переменные среды **конфигурации** > **параметры приложения** страницы на портале Azure.

Затем определите, должен ли источник данных быть доступным для одного приложения или для всех приложений, работающих в сервлете Tomcat.

#### <a name="application-level-data-sources"></a>Источники данных на уровне приложения

1. Создайте файл `context.xml` в каталоге `META-INF/` вашего проекта. Создайте каталог `META-INF/`, если он не существует.

2. В `context.xml` добавьте элемент `Context`, чтобы связать источник данных с адресом JNDI. Замените заполнитель `driverClassName` именем класса драйвера из приведенной выше таблицы.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ```

3. Обновите `web.xml` приложения для использования источника данных в этом приложении.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Общие ресурсы на уровне сервера

1. Скопируйте содержимое `/usr/local/tomcat/conf` в папку `/home/tomcat/conf` на экземпляре службы приложений для Linux с помощью SSH, если на нем еще нет конфигурации.
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Добавьте элемент Context в файл `server.xml` в элементе `<Server>`.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. Обновите `web.xml` приложения для использования источника данных в этом приложении.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Завершение конфигурации

Наконец установите драйвер JAR-файлы в пути к классам Tomcat и перезапустите службу приложений.

1. Убедитесь, что файлы драйвера JDBC доступны для загрузчика классов Tomcat, разместив их в каталоге `/home/tomcat/lib`. (Если этот каталог отсутствует, создайте его.) Чтобы передать эти файлы в экземпляр службы приложений, выполните следующие действия.
    1. В [Cloud Shell](https://shell.azure.com), установить расширение веб-приложения:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Выполните следующую команду интерфейса командной строки, чтобы создать туннель SSH из локальной системы в службе приложений:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Подключитесь к локальному порту туннелирования с помощью клиента SFTP и передайте эти файлы в папку `/home/tomcat/lib`.

    Кроме того, драйвер JDBC можно отправить с помощью FTP-клиента. Чтобы получить учетные данные FTP, следуйте этим [инструкциям](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Если вы создали источник данных на уровне сервера, перезапустите приложение Linux службы приложений. Tomcat сбросит `CATALINA_HOME` до значения `/home/tomcat/conf` и будет использовать обновленную конфигурацию.

### <a name="spring-boot"></a>Spring Boot

Чтобы подключиться к источникам данных в приложениях Spring Boot, мы рекомендуем Создание строк подключения и вставляя их в вашей `application.properties` файл.

1. В разделе «Конфигурация» страницы службы приложений задайте имя для строки, вставьте строку подключения JDBC в соответствующее поле и задайте для этого типа «Custom». Можно дополнительно задать эту строку подключения как параметр слота.

    Эта строка подключения имеет доступ к нашему приложению как переменную среды с именем `CUSTOMCONNSTR_<your-string-name>`. Например, строка подключения, созданных ранее будет называться `CUSTOMCONNSTR_exampledb`.

2. В вашей `application.properties` файл, ссылаются на эту строку подключения с именем переменной среды. В нашем примере мы используем следующее выражение.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

См. в разделе [документации Spring Boot на доступ к данным](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) и [выразили конфигураций](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) Дополнительные сведения по этой теме.

## <a name="configure-java-ee-wildfly"></a>Настройка Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition на служба приложений в Linux доступна в предварительной версии. Стек **не** рекомендуется использовать для работы с выходом в рабочей среде. сведения о наших стеков Java SE и Tomcat.

Служба приложений Azure на платформе Linux позволяет разработчикам Java создавать, развертывать и масштабировать приложения Java, Enterprise (Java EE) на полностью управляемую службу под управлением Linux.  Базовой средой выполнения Java Enterprise является сервер приложений [Wildfly](https://wildfly.org/) в открытым кодом.

[Масштабирование с помощью службы приложений](#scale-with-app-service)
[конфигурации сервера приложений Настройка](#customize-application-server-configuration)
[модули и зависимости](#modules-and-dependencies)
[данных источники](#data-sources)
[включения обмена сообщениями поставщиков](#enable-messaging-providers)
[настроить кэширование сеанса управления](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Масштабирование с помощью Службы приложений

Сервер приложений WildFly работает в Службе приложений на платформе Linux в автономном режиме, а не в конфигурации домена. При масштабировании плана службы приложений каждый экземпляр WildFly настраивается как отдельный сервер.

 Вы можете выполнять вертикальное или горизонтальное масштабирование своего приложения, придерживаясь [правил масштабирования](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) и [увеличивая число экземпляров](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Настройка параметров сервера приложений

В экземплярах веб-приложений не отслеживается состояние, поэтому каждый новый экземпляр необходимо настроить при запуске для поддержки конфигурации WildFly, необходимой для приложения.
Вы можете написать скрипт запуска Bash для вызова интерфейса командной строки WildFly и выполнения таких действий:

- настройка источников данных;
- настройка поставщиков службы обмена сообщениями;
- добавление других модулей и зависимостей Wildfly в конфигурацию сервера.

 Скрипт выполняется после запуска Wildfly, но перед запуском приложения. Для настройки запущенного сервера приложений с применением требуемых конфигурации или изменений скрипт должен использовать [интерфейс командной строки JBOSS](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface), вызываемый из `/opt/jboss/wildfly/bin/jboss-cli.sh`.

Не используйте для настройки Wildfly интерактивный режим интерфейса командной строки. Вместо этого можно передать скрипт с командами в интерфейс командной строки JBoss с помощью команды `--file`, например:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Отправьте скрипт запуска в каталог `/home/site/deployments/tools` своего экземпляра Службы приложений. См. инструкции по [получению учетных данных FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope).

Укажите в поле **Сценарий запуска** на портале Azure расположение скрипта запуска оболочки, например `/home/site/deployments/tools/your-startup-script.sh`.

Укажите [параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) в конфигурацию приложения, чтобы передать переменные среды для использования в скрипте. В параметрах приложения хранятся строки подключения и другие секретные данные, необходимые для настройки приложения из системы управления версиями.

### <a name="modules-and-dependencies"></a>Модули и зависимости

Чтобы установить модули и связанные зависимости в пути к классам Wildfly из интерфейса командной строки JBoss, нужно создать следующие файлы в соответствующем каталоге. Для некоторых модулей и зависимостей может понадобиться дополнительная настройка (например, JNDI или других API). Следующий список включает минимальный набор параметров для настройки зависимостей, применимый в большинстве случаев.

- [Дескриптор модуля XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Этот XML-файл определяет имя, атрибуты и зависимости вашего модуля. Этот [пример файла module.xml](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) определяет модуль Postgres, зависимость JDBC (файл с расширением .jar) и другие требуемые зависимости модуля.
- Все необходимые зависимости для вашего модуля (файлы с расширением .jar).
- Скрипт с командами интерфейса командной строки JBoss CLI для настройки нового модуля. Этот файл будет содержать команды для выполнения в интерфейсе командной строки JBoss, благодаря которым сервер сможет использовать зависимости. См. [документацию по командам для добавления модулей, источников данных и поставщиков службы обмена сообщениями ](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Скрипт запуска Bash для вызова интерфейса командной строки JBoss и выполнения скрипта из предыдущего шага. Этот файл будет выполняться при перезапуске экземпляра Службы приложений или подготовке новых экземпляров в ходе горизонтального масштабирования. Этот скрипт запуска позволяет реализовать и другие конфигурации для приложения, так как команды JBoss передаются в интерфейс командной строки JBoss. Как минимум, этот файл можно использовать как отдельную команду для передачи соответствующего скрипта JBoss в интерфейс командной строки JBoss:

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

Получив файлы и содержимое для модуля, выполните следующие действия, чтобы добавить модуль Wildfly на сервер приложений.

1. Передайте файлы по FTP в каталог `/home/site/deployments/tools` своего экземпляра Службы приложений. См. инструкции по получению учетных данных FTP.
2. В **конфигурации** > **Общие параметры** страницу портала, задайте «сценарий запуска» Azure поле к расположению сценария запуска оболочки, например `/home/site/deployments/tools/your-startup-script.sh` .
3. Перезапустите экземпляр службы приложений, нажав клавишу **перезапустите** кнопку **Обзор** раздела на портале или с помощью Azure CLI.

### <a name="configure-data-source-connections"></a>Настройка соединения с источниками данных

Процесс настройки Wildfly для подключения к источнику данных аналогичен действиям, описанным выше в разделе "Установка модулей и зависимостей". Эти действия можно выполнять для любой службы базы данных Azure.

1. Скачайте драйвер JDBC для своей версии базы данных. Отсюда можно скачать драйверы для [Postgres](https://jdbc.postgresql.org/download.html) и [MySQL](https://dev.mysql.com/downloads/connector/j/). Распакуйте архив, чтобы получить файл с расширением .jar.
2. Выполните действия, описанные в разделе "Модули и зависимости", чтобы создать и передать дескриптор модуля XML, скрипт интерфейса командной строки JBoss CLI, скрипт запуска и JDBC-зависимость (файл с расширением .jar).

См. дополнительные сведения о настройке Wildfly для использования с [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) и [Базы данных SQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898). Чтобы добавить определения источников данных на сервер, можно использовать разные подходы.

### <a name="enable-messaging-providers"></a>Включение обмена сообщениями поставщиков

Чтобы включить компоненты, управляемые сообщениями (Message Driven Beans), используя в качестве механизма обмена сообщениями Служебную шину, сделайте следующее:

1. Используйте [библиотеку обмена сообщениями Apache QPId JMS](https://qpid.apache.org/proton/index.html). Включите эту зависимость в файл pom.xml (или другой файл сборки) для приложения.

2. Создайте [ресурсы Служебной шины](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Создайте пространство имен Служебной шины Azure и очередь в пределах этого пространства имен, а также политику общего доступа с возможностью отправки и получения.

3. Передайте ключ политики общего доступа в код путем URL-кодирования первичного ключа политики или [используйте для этого пакет SDK Служебной шины](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Выполните шаги, описанные в разделе "Установка модулей и зависимостей", используя дескриптор модуля XML, зависимости .jar, команды интерфейса командной строки JBoss и скрипт запуска для поставщика JMS. В дополнение к существующим четырем файлам вам также нужно создать XML-файл, определяющий имя JNDI для очереди и раздела JMS. См. дополнительные сведения о соответствующих [файлах конфигурации](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig).

### <a name="configure-session-management-caching"></a>Настройка кэширования управления сеансами

По умолчанию Служба приложений в Linux будет использовать файлы cookie сходства сеансов, чтобы убедиться, что клиентские запросы с существующими сеансами направляются в один и тот же экземпляр приложения. Это поведение по умолчанию не требует настройки, но имеет некоторые ограничения:

- Если экземпляр приложения перезапущен или его масштаб уменьшен, состояние сеанса пользователя на сервере приложений будет утеряно.
- Если для приложений настроены параметры длительного времени ожидания сеанса или фиксированное число пользователей, автомасштабирование новых экземпляров для получения нагрузки может занять некоторое время, так как только новые сеансы будут направляться в новые запущенные экземпляры.

Вы можете настроить Wildfly для использования внешнего хранилища сеанса, например [кэша Azure для Redis](/azure/azure-cache-for-redis/). Вам нужно будет [отключить сходство существующих экземпляров ARR](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/), чтобы отключить маршрутизацию на основе файлов cookie для сеанса и обеспечить бесперебойную работу настроенного хранилища сеансов Wildfly.

## <a name="docker-containers"></a>контейнеры Docker;

Чтобы использовать в своих контейнерах поддерживаемый в Azure пакет JDK Zulu, обязательно используйте готовые образы, предоставленные на [странице скачивания Azul Zulu Enterprise для Azure](https://www.azul.com/downloads/azure-only/zulu/), или используйте примеры `Dockerfile` из [репозитория Майкрософт для Java на сайте GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Заявление о поддержке

### <a name="runtime-availability"></a>Доступность во время выполнения

Служба приложений для Linux поддерживает две среды выполнения для управляемого размещения веб-приложений Java.

- [Контейнер сервлетов Tomcat](https://tomcat.apache.org/) для запуска приложений, которые упакованы как файлы веб-архива (WAR-файлы). Поддерживаются версии 8.5 и 9.0.
- Среда выполнения Java SE для запуска приложений, которые упакованы как файлы архива Java (JAR-файлы). Поддерживаемые версии: Java 8 и 11.

### <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Сборка OpenJDK типа Azul Zulu Enterprise — это бесплатный мультиплатформенный, готовый дистрибутив OpenJDK для Azure и Azure Stack, который поддерживается корпорацией Майкрософт и Azul Systems. Они содержат все компоненты для сборки и запуска приложений Java SE. Вы можете установить JDK по [установки Java JDK](https://aka.ms/azure-jdks).

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре.

### <a name="security-updates"></a>обновления для системы безопасности;

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

## <a name="next-steps"></a>Дальнейшие действия

Посетите центр [Azure для разработчиков Java](/java/azure/), чтобы найти краткие руководства Azure, руководства и справочную документацию по Java.

Ответы на общие вопросы об использовании службы приложений для Linux, не относящиеся к разработке для Java, можно найти в разделе [вопросов и ответов о службе приложений для Linux](app-service-linux-faq.md).

