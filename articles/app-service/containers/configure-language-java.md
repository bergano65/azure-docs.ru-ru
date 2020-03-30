---
title: Настройка приложений Linux Java
description: Узнайте, как настроить предварительно построенный контейнер Java для вашего приложения. В этой статье показаны наиболее распространенные задачи настройки.
keywords: лазурный сервис приложений, веб-приложение, linux, oss, Java, Java ee, jee, javae
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 970701606811cbd61a9bfebe39ff82cdc91d5693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245843"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Настройка приложения Java в Linux для Службы приложений Azure

Служба приложений Azure на Linux позволяет Java-разработчикам быстро создавать, развертывать и масштабировать свои упакованные веб-приложения Tomcat или Java Standard Edition (SE) на полностью управляемом сервисе на базе Linux. Возможно развертывание приложений с подключаемыми модулями Maven из командной строки или в редакторах, например Visual Studio Code, Eclipse или IntelliJ.

В этом руководстве содержатся ключевые концепции и инструкции для Java-разработчиков, которые используют встроенный контейнер Linux в App Service. Если вы никогда не пользовались службой приложений Azure, следуйте [за java-запуском.](quickstart-java.md)

## <a name="deploying-your-app"></a>Развертывание приложения

Для развертывания файлов .jar и .war можно использовать [Maven Plugin для службы приложений Azure.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) Развертывание с популярными IDEs также поддерживается [С помощью Azure Toolkit для IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) или [Azure Toolkit для Eclipse.](/java/azure/eclipse/azure-toolkit-for-eclipse)

В противном случае метод развертывания будет зависеть от типа архива:

- Чтобы развернуть файлы WAR в Tomcat, используйте конечную точку `/api/wardeploy/` для публикации файла архива. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Чтобы развернуть файлы JAR в образах Java SE, используйте конечную точку `/api/zipdeploy/` сайта Kudu. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Не развертывайте файлы WAR или JAR с помощью FTP. Средство FTP предназначено для передачи сценариев запуска, зависимостей или других файлов среды выполнения. Оно не является оптимальным решением для развертывания веб-приложений.

## <a name="logging-and-debugging-apps"></a>Ведение журнала и отладка приложений

Отчеты о производительности, визуализация трафика и проверка работоспособности доступны на портале Azure для каждого приложения. Для получения дополнительной информации смотрите [обзор диагностики службы приложений Azure](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Доступ к консоли SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Для получения дополнительной информации смотрите [журналы Stream в облачной оболочке](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Ведение журнала приложений

Включите [ведение журнала приложений](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) с помощью портала Azure или [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config), чтобы настроить службу приложений для записи выходных данных стандартной консоли приложения и потоков ошибок стандартной консоли в локальную файловую систему или хранилище BLOB-объектов Azure. Запись журналов в локальную файловую систему экземпляра службы приложений отключается через 12 часов после настройки ведения журнала. Если необходимо более длительное хранение, настройте приложение для записи выходных данных в контейнер больших двоичных объектов. Ваши журналы приложений Java и Tomcat можно найти в *каталоге /home/LogFiles/Application/Application/.*

Если приложение использует [Logback](https://logback.qos.ch/) или [Log4j](https://logging.apache.org/log4j) для трассировки, то эти данные трассировки можно передать в Azure Application Insights для просмотра, выполнив инструкции по настройке платформы ведения журнала в разделе [Просмотр журналов трассировки Java в Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Средства диагностики

Встроенные изображения Java основаны на операционной системе [Alpine Linux.](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Используйте `apk` менеджер пакетов для установки любых инструментов или команд для устранения неполадок.

### <a name="flight-recorder"></a>«Черный ящик»

Все изображения Linux Java в App Service установлены в пулу Flight Recorder, чтобы вы могли легко подключиться к JVM и начать запись профайлера или создать свалку кучи.

#### <a name="timed-recording"></a>Приуроченная запись

Чтобы начать работу, SSH в службу приложений и запустите `jcmd` команду, чтобы увидеть список всех запущенных процессов Java. В дополнение к jcmd себя, вы должны увидеть ваше приложение Java работает с идентификатором процесса номер (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Выполните команду ниже, чтобы начать 30-секундную запись JVM. Это позволит профилю JVM и создать файл JFR имени *jfr_example.jfr* в домашнем каталоге. (Заменить 116 с pid вашего приложения Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Во время 30-секундного интервала, вы можете `jcmd 116 JFR.check`проверить запись происходит путем запуска. Это покажет все записи для данного процесса Java.

#### <a name="continuous-recording"></a>Непрерывная запись

Вы можете использовать бортовой самописец зулуса для непрерывного профилировать свое java-приложение с минимальным влиянием на производительность выполнения[(источник).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Для этого запустите следующую команду Azure CLI для создания настройки приложения, названной JAVA_OPTS с необходимой конфигурацией. Содержимое JAVA_OPTS настройки приложения передаются `java` команде при начале работы приложения.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

После начала записи можно в любое время сбросить `JFR.dump` текущие данные записи с помощью команды.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Для получения дополнительной информации, пожалуйста, смотрите [ссылку командования Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Анализ записей

Используйте [FTPS,](../deploy-ftp.md) чтобы загрузить файл JFR на локальную машину. Чтобы проанализировать файл JFR, загрузите и установите [зулу Сулу Управления полетами](https://www.azul.com/products/zulu-mission-control/). Для получения инструкций по управлению [Azul documentation](https://docs.azul.com/zmc/) полетами Зулу см. [installation instructions](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

## <a name="customization-and-tuning"></a>Настройка

Служба приложений Azure для Linux поддерживает настройку и настройку коробки через портал Azure и CLI. Просмотрите следующие статьи для конфигурации веб-приложений, не связанных с Java:

- [Настройка параметров приложения](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Настройка личного домена](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Настройка SSL-привязок](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Добавление CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Настройка сайта Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Настройка параметров среды выполнения Java

Чтобы установить выделенную память или другие параметры выполнения JVM в средах Tomcat и Java SE, создайте [настройку приложения](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) с именем `JAVA_OPTS` опций. При запуске служба приложений для Linux передает этот параметр в качестве переменной среды в среду выполнения Java.

На портале Azure в разделе **Параметры приложения** для веб-приложения создайте параметр приложения `JAVA_OPTS`, включающий в себя дополнительные параметры, такие как `-Xms512m -Xmx1204m`.

Чтобы настроить настройку приложения из плагина Maven, добавьте теги настройки/значения в раздел плагина Azure. В следующем примере устанавливается определенный минимальный и максимальный размер кучи Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Разработчики, запускающие отдельное приложение в одном слоте развертывания в плане службы приложений, могут использовать следующие параметры.

- Экземпляры B1 и S1:`-Xms1024m -Xmx1024m`
- Экземпляры B2 и S2:`-Xms3072m -Xmx3072m`
- Экземпляры B3 и S3:`-Xms6144m -Xmx6144m`

При настройке параметров кучи приложения просмотрите сведения о плане службы приложений и примите во внимание, что наличие нескольких приложений и одного слота развертывания требует оптимального выделения памяти.

Если вы развертываете приложение JAR, оно должно быть названо *app.jar,* чтобы встроенное изображение скорректировало ваше приложение. (Плагин Maven делает это переименование автоматически.) Если вы не хотите переименовывать jar в *app.jar,* вы можете загрузить сценарий оболочки с командой для запуска JAR. Затем вставьте полный путь к этому скрипту в текстовое поле [Файл запуска](app-service-linux-faq.md#built-in-images) в разделе конфигурации на портале. Скрипт запуска не выполняется в каталоге, в который он помещен. Поэтому всегда используйте абсолютные пути для создания ссылок на файлы в скрипте запуска (например, `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>Отрегулируйте тайм-аут запуска

Если java-приложение особенно велико, следует увеличить срок запуска. Для этого создайте настройку `WEBSITES_CONTAINER_START_TIME_LIMIT` приложения и установите его на количество секунд, которые Служба приложения должна подождать до того, как выйти из тайм-аута. Максимальное значение `1800` - секунды.

### <a name="pre-compile-jsp-files"></a>Предварительное составление файлов JSP

Чтобы повысить производительность приложений Tomcat, вы можете компилировать файлы JSP перед развертыванием в Службе приложений. Вы можете использовать [плагин Maven,](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) предоставленный Apache Sling, или с помощью этого [файла сборки ant.](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)

## <a name="secure-applications"></a>Защита приложений

Для приложений Java, работающих в службе приложений для Linux, предлагается тот же набор [рекомендаций по обеспечению безопасности](/azure/security/security-paas-applications-using-app-services), что и для других приложений.

### <a name="authenticate-users-easy-auth"></a>Аутентификации пользователей (Easy Auth)

Настройка аутентификации приложений на портале Azure с опцией **аутентификации и авторизации.** Вы можете включить аутентификацию с помощью Azure Active Directory или имен для входа в социальные сети, таких как Facebook, Google или GitHub. На портале Azure можно настроить только один поставщик аутентификации. Дополнительные сведения приведены в разделе [Настройка приложения службы приложений для использования входа с помощью Azure Active Directory](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) и связанных статьях о других поставщиках удостоверений. Если необходимо включить несколько поставщиков входа, следуйте инструкциям в статье [Настройка проверки подлинности и авторизации в службе приложений Azure](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json).

#### <a name="tomcat"></a>Tomcat

Ваше приложение Tomcat может получить доступ к претензиям пользователя непосредственно из сервлета, отбросив основной объект на объект Карты. Объект Карты будет отображать каждый тип претензии в коллекции претензий для этого типа. В коде `request` ниже, является `HttpServletRequest`экземпляром .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Теперь вы `Map` можете проверить объект для любой конкретной претензии. Например, следующий фрагмент кода итерирует все типы претензий и печатает содержимое каждой коллекции.

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

Чтобы подписать пользователей, `/.auth/ext/logout` используйте путь. Для выполнения других действий, пожалуйста, ознакомьтесь с документацией по [использованию аутентификации и авторизации службы приложений.](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) Существует также официальная документация по интерфейсу Tomcat [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) и его методам. Следующие методы сервопривода также увлажняются в зависимости от конфигурации службы приложений:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Чтобы отключить эту функцию, создайте `WEBSITE_AUTH_SKIP_PRINCIPAL` настройку `1`приложения с именем со значением . Чтобы отключить все фильтры сервопривода, добавленные `WEBSITE_SKIP_FILTERS` Службой App, создайте настройку с именем `1`.

#### <a name="spring-boot"></a>Spring Boot

Разработчики для Spring Boot могут использовать [краткое руководство по использованию Spring Boot и Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable), чтобы защитить приложения с помощью привычных заметок и интерфейсов API Spring Security. Не забудьте увеличить максимальный размер заголовка в файле *application.properties.* Мы рекомендуем использовать значение `16384`.

### <a name="configure-tlsssl"></a>Настройка TLS/SSL

Следуйте инструкциям в [Secure пользовательское имя DNS с привязкой SSL в Azure App Service,](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) чтобы загрузить существующий сертификат SSL и привязать его к доменному имени вашего приложения. По умолчанию приложение по-прежнему будет разрешать HTTP-подключения. Выполните соответствующие инструкции в этом руководстве, чтобы принудительно включить SSL и TLS.

### <a name="use-keyvault-references"></a>Использование ссылок KeyVault

[Azure KeyVault](../../key-vault/key-vault-overview.md) предоставляет централизованное секретное управление политиками доступа и историей аудита. Вы можете хранить секреты (например, пароли или строки соединения) в KeyVault и получать доступ к этим секретам в приложении через переменные среды.

Во-первых, следуйте инструкциям по [предоставлению вашему приложению доступа к Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) и [внесению ссылки на ваш секрет в настройке приложения.](../app-service-key-vault-references.md#reference-syntax) Можно проверить, что ссылка разрешает секрет, печатая переменную среды при удаленном доступе к терминалу Службы приложения.

Чтобы привить эти секреты в файл конфигурации Spring`${MY_ENV_VAR}`или Tomcat, используйте синтаксис с переменной впрыски среды (). Для файлов конфигурации Spring, пожалуйста, ознакомьтесь с этой документацией по [экстернализованным конфигурациям.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

### <a name="using-the-java-key-store"></a>Использование магазина ключей Java

По умолчанию любые государственные или частные сертификаты, загруженные в [App Service Linux,](../configure-ssl-certificate.md) будут загружены в соответствующие ключевые магазины Java по мере запуска контейнера. После загрузки сертификата необходимо перезапустить службу приложений, чтобы она была загружена в магазин Java Key Store. Публичные сертификаты загружаются `$JAVA_HOME/jre/lib/security/cacerts`в Key Store, а `$JAVA_HOME/lib/security/client.jks`частные сертификаты хранятся в .

Дополнительная конфигурация может потребоваться для шифрования соединения JDBC с сертификатами в Магазине ключей Java. Пожалуйста, обратитесь к документации для выбранного драйвера JDBC.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [Mysql](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [Mongodb](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Инициализация магазина java Key

Чтобы инициализировать `import java.security.KeyStore` объект, загрузите файл клавиатуры паролем. Пароль по умолчанию для обоих ключевых магазинов является "изменением".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Ручная загрузка ключевого хранилища

Сертификаты можно загрузить вручную в хранилище ключей. Создайте настройку `SKIP_JAVA_KEYSTORE_LOAD`приложения, со `1` значением для автоматической загрузки Службы App От загрузки сертификатов в ключевой магазин. Все публичные сертификаты, загруженные в Службу `/var/ssl/certs/`Приложений через портал Azure, хранятся под . Частные сертификаты `/var/ssl/private/`хранятся под .

Вы можете взаимодействовать или отлажовать инструмент Java Key, открыв соединение `keytool` [SSH](app-service-linux-ssh-support.md) с службой app и запустив команду. Ознакомьтесь с [документацией Key Tool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) для списка команд. Для получения дополнительной информации о API KeyStore, пожалуйста, обратитесь к [официальной документации](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Настройка платформ APM

В этом разделе показано, как подключить Java-приложения, развернутые в Службе приложений Azure на Linux, с платформами мониторинга производительности приложений NewRelic и AppDynamics (APM).

### <a name="configure-new-relic"></a>Настройка New Relic

1. Создайте учетную запись NewRelic на сайте [NewRelic.com](https://newrelic.com/signup)
2. Скачать Java-агент от NewRelic, он будет иметь имя файла похож на *newrelic-java-x.x.x.zip*.
3. Скопируйте ключ лицензии. Он понадобиться позже для настройки агента.
4. [SSH в экземпляр службы приложений](app-service-linux-ssh-support.md) и создайте новый каталог */дом/сайт/wwwroot/apm.*
5. Загрузите распакованные файлы агента NewRelic Java в каталог *под /home/site/wwwroot/apm*. Файлы для вашего агента должны быть в */дома /сайт / wwwroot/apm/newrelic*.
6. Измените файл YAML по адресу */home/site/wwwroot/apm/newrelic/newrelic.yml* и замените стоимость лицензии заполнителя своим собственным ключом лицензии.
7. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если приложение использует **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Настройка AppDynamics

1. Создайте учетную запись AppDynamics на сайте [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Скачать Java-агент с веб-сайта AppDynamics, имя файла будет похоже на *Имя AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH в экземпляр службы приложений](app-service-linux-ssh-support.md) и создайте новый каталог */дом/сайт/wwwroot/apm.*
4. Загрузите файлы Java-агента в каталог *под /home/site/wwwroot/apm*. Файлы для вашего агента должны быть в */дома/сайте/wwwroot/apm/appdynamics.*
5. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если вы используете **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.

> [!NOTE]
> Если у вас уже есть переменная среды `JAVA_OPTS` или `CATALINA_OPTS`, добавьте параметр `-javaagent:/...` в конец текущего значения.

## <a name="configure-jar-applications"></a>Настройка приложений JAR

### <a name="starting-jar-apps"></a>Запуск приложений JAR

По умолчанию Служба приложения ожидает, что ваше приложение JAR будет называться *app.jar.* Если у него есть это имя, оно будет запущено автоматически. Для пользователей Maven вы можете установить имя JAR, включив `<finalName>app</finalName>` в `<build>` раздел *pom.xml.* [Вы можете сделать то же самое в Gradle,](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) установив свойство. `archiveFileName`

Если вы хотите использовать другое имя для JAR, вы также должны предоставить [команду запуска,](app-service-linux-faq.md#built-in-images) которая выполняет ваш файл JAR. Например, `java -jar my-jar-app.jar`. Вы можете установить значение команды запуска на портале, под конфигурацией > общими настройками или с именем `STARTUP_COMMAND`Настройка приложения.

### <a name="server-port"></a>Порт сервера

App Service Linux маршруты входящих запросов на порт 80, так что ваше приложение должно слушать на порту 80, а также. Это можно сделать в конфигурации приложения (например, файле *приложения Spring.properties)* или `java -jar spring-app.jar --server.port=80`в startup Command (например, в). Пожалуйста, ознакомьтесь со следующей документацией для общих инфраструктур Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [СпаркДжаа](http://sparkjava.com/documentation#embedded-web-server)
- [Микронавт](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Платформа воспроизведения](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Верткс](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Кваркус](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Источники данных

### <a name="tomcat"></a>Tomcat

Эти инструкции применимы ко всем подключениям к базе данных. Необходимо будет заменить значения заполнителей на имя класса драйвера и JAR-файл выбранной базы данных. Ниже приведена таблица с именами классов и ссылками для скачивания драйверов для распространенных баз данных.

| База данных   | Имя класса драйвера                             | Драйвер JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Скачать](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Скачать](https://dev.mysql.com/downloads/connector/j/) (выберите "Platform Independent" (Независимо от платформы)) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Скачать](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Для настройки Tomcat для использования Java Database Connectivity (JDBC) или API Java `CATALINA_OPTS` Persistence API (JPA) сначала настройте переменную среды, которая читается Tomcat при запуске. Задайте эти значения с помощью параметра приложения в [подключаемом модуле Maven для службы приложений](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Или установите переменные среды на странице**настройки** **конфигурации** > на портале Azure.

Затем определите, должен ли источник данных быть доступным для одного приложения или для всех приложений, работающих в сервлете Tomcat.

#### <a name="application-level-data-sources"></a>Источники данных на уровне приложений

1. Создайте файл *context.xml* в *каталоге META-INF/каталоге* вашего проекта. Создайте каталог *META-INF/,* если его не существует.

2. В *context.xml*добавьте `Context` элемент, связывающий источник данных с адресом JNDI. Замените заполнитель `driverClassName` именем класса драйвера из приведенной выше таблицы.

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

3. Обновление *веб-xml* приложения, чтобы использовать источник данных в приложении.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Общие ресурсы уровня сервера

Добавление общего источника данных на уровне сервера потребует от вас отсвагиваемого сервера Tomcat. Во-первых, загрузите [сценарий запуска](app-service-linux-faq.md#built-in-images) и установите путь к скрипту в **команде запуска конфигурации.** > **Startup Command** Вы можете загрузить сценарий запуска с помощью [FTP](../deploy-ftp.md).

Ваш сценарий запуска сделает [xsl преобразование](https://www.w3schools.com/xml/xsl_intro.asp) в файл server.xml `/usr/local/tomcat/conf/server.xml`и вывести полученный файл xml. Сценарий запуска должен установить libxslt через apk. Ваш файл xsl и сценарий запуска могут быть загружены через FTP. Ниже приведен пример сценария запуска.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Приведен пример файла xsl ниже. Файл примера xsl добавляет новый узла разъема к серверу Tomcat.xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Завершение конфигурации

Наконец, поместите драйвер ЫРА в классTo Tomcat и перезапустите службу приложений.

1. Убедитесь, что файлы драйверов JDBC доступны для класса Tomcat, разместив их в *каталоге /home/tomcat/lib.* (Создайте этот каталог, если он еще не существует.) Чтобы загрузить эти файлы в экземпляр Службы приложения, выполните следующие действия:

    1. В [облачной оболочке](https://shell.azure.com)установите расширение webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Запустите следующую команду CLI, чтобы создать туннель SSH из локальной системы в Службу приложений:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Подключитесь к локальному туннелирующему порту с клиентом SFTP и загрузите файлы в *папку /home/tomcat/lib.*

    Кроме того, драйвер JDBC можно отправить с помощью FTP-клиента. Чтобы получить учетные данные FTP, следуйте этим [инструкциям](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Если вы создали источник данных на уровне сервера, перезапустите приложение Linux службы приложений. Tomcat сбросит `CATALINA_BASE` до значения `/home/tomcat` и будет использовать обновленную конфигурацию.

### <a name="spring-boot"></a>Spring Boot

Чтобы подключиться к источникам данных в приложениях Spring Boot, мы предлагаем создать строки соединения и ввести их в файл *application.properties.*

1. В разделе "Конфигурация" страницы службы приложения установите имя строки, вставьте строку соединения JDBC в поле значения и установите тип "Custom". Можно дополнительно установить строку соединения в качестве настройки слота.

    Эта строка соединения доступна для нашего `CUSTOMCONNSTR_<your-string-name>`приложения в качестве переменной среды под названием . Например, строка соединения, созданная `CUSTOMCONNSTR_exampledb`выше, будет названа.

2. В файле *application.properties* ссылайтесь на эту строку связи с переменным именем среды. В нашем примере мы хотели бы использовать следующее.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Для получения дополнительной информации по этой теме можно ознакомиться с [документацией Spring Boot о доступе к данным](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) и [внеменяемых конфигурациях.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Используйте Redis в качестве кэша сеанса с Tomcat

Можно настроить Tomcat для использования внешнего хранилища сеансов, например [Azure Cache для Redis.](/azure/azure-cache-for-redis/) Это позволяет сохранить состояние сеанса пользователя (например, данные корзины покупок) при переводе пользователя в другой экземпляр приложения, например, при автоматическом масштабировании, перезагрузке или сбое.

Чтобы использовать Tomcat с Redis, необходимо настроить приложение для использования реализации [PersistentManager.](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) Следующие шаги объясняют этот процесс с помощью [Pivotal Session Manager: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) в качестве примера.

1. Откройте терминал Bash `<variable>=<value>` и используйте для установки каждой из следующих переменных среды.

    | Переменная                 | Значение                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Название группы ресурсов, содержащей экземпляр службы app.       |
    | WEBAPP_NAME              | Название экземпляра службы приложений.                                     |
    | WEBAPP_PLAN_NAME         | Название плана службы приложений.                                         |
    | РЕГИОН                   | Название региона, в котором размещается ваше приложение.                           |
    | REDIS_CACHE_NAME         | Имя кэша Azure для экземпляра Redis.                           |
    | REDIS_PORT               | Порт SSL, который слушает ваш кэш Redis.                             |
    | REDIS_PASSWORD           | Основной ключ доступа для вашего экземпляра.                                  |
    | REDIS_SESSION_KEY_PREFIX | Значение, указанное для определения ключей сеанса, которые поступают из приложения. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    Вы можете найти информацию об имени, порту и доступе на портале Azure, загнав **разделы ключей** **свойств** или доступа в экземпляре службы.

2. Создайте или обновите файл *src/main/webapp/META-INF/context.xml* со следующим содержанием:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Этот файл определяет и настраивает реализацию диспетчера сеансов для вашего приложения. Он использует переменные среды, установленные на предыдущем этапе, чтобы сохранить информацию учетной записи из исходных файлов.

3. Используйте FTP, чтобы загрузить файл JAR менеджера сеанса в экземпляр Службы приложений, поместив его в *каталог /home/tomcat/lib.* Для получения дополнительной информации [см. Развертывание приложения в службе приложений Azure с помощью FTP/S.](https://docs.microsoft.com/azure/app-service/deploy-ftp)

4. Отоверьте [файлcookieо-файлы сродства сеанса](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) для экземпляра службы приложений. Это можно сделать с портала Azure, перенаправившись в приложение, а затем установив **настройки > общих настроек > ARR сродство** к **Off.** Кроме того, можно использовать следующую команду:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    По умолчанию Служба app Service будет использовать файлы cookie-файлов с родством сеансов, чтобы гарантировать, что запросы клиентов с существующими сеансами будут направляться в тот же экземпляр приложения. Это поведение по умолчанию не требует конфигурации, но оно не может сохранить состояние сеанса пользователя при перезапущени экземпляре приложения или при перенаправлении трафика в другой экземпляр. При [отключении существующей конфигурации ARR Instance Affinity](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) для выключения функции "сеанс-куки" позволяет настроенный магазин сеансов работать без помех.

5. Перейдите в раздел **Свойства** экземпляра Службы app и найдите **дополнительные исходящие IP-адреса.** Они представляют все возможные исходящие IP-адреса для вашего приложения. Скопируйте их для использования на следующем этапе.

6. Для каждого IP-адреса создайте правило брандмауэра в кэше Azure для экземпляра Redis. Это можно сделать на портале Azure из раздела **Firewall** экземпляра Redis. Предоставьте уникальное имя для каждого правила и установите значения **IP-адреса Start** и **Ip-адреса End** на один и тот же IP-адрес.

7. Перейдите к разделу **Расширенные настройки** экземпляра Redis и **установите разрешить доступ только через SSL** к **No.** Это позволяет экземпляру службы приложений общаться с кэшем Redis через инфраструктуру Azure.

8. Обновление `azure-webapp-maven-plugin` конфигурации в файле *pom.xml* вашего приложения для обозначения информации об учетной записи Redis. Этот файл использует переменные среды, установленные ранее, чтобы сохранить информацию учетной записи из исходных файлов.

    При необходимости измените `1.7.0` на текущую версию [подключаемого модуля Maven для Службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Восстановить и передислоцировать приложение.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Приложение теперь будет использовать кэш Redis для управления сеансами.

Для примера, который можно использовать для тестирования этих инструкций, см. репо GitHub с [масштабированием-штатно-вакантно-java-web-app-on-azure.](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure)

## <a name="docker-containers"></a>контейнеры Docker;

Чтобы использовать в своих контейнерах поддерживаемый в Azure пакет JDK Zulu, обязательно используйте готовые образы, предоставленные на [странице скачивания Azul Zulu Enterprise для Azure](https://www.azul.com/downloads/azure-only/zulu/), или используйте примеры `Dockerfile` из [репозитория Майкрософт для Java на сайте GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Заявление о поддержке

### <a name="runtime-availability"></a>Доступность продолжительности выполнения

Служба приложений для Linux поддерживает две среды выполнения для управляемого размещения веб-приложений Java.

- [Контейнер сервлетов Tomcat](https://tomcat.apache.org/) для запуска приложений, которые упакованы как файлы веб-архива (WAR-файлы). Поддерживаются версии 8.5 и 9.0.
- Среда выполнения Java SE для запуска приложений, которые упакованы как файлы архива Java (JAR-файлы). Поддерживаемые версии Java 8 и 11.

### <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Сборка OpenJDK типа Azul Zulu Enterprise — это бесплатный мультиплатформенный, готовый дистрибутив OpenJDK для Azure и Azure Stack, который поддерживается корпорацией Майкрософт и Azul Systems. Они содержат все компоненты для сборки и запуска приложений Java SE. Вы можете установить JDK из [Установки Java JDK.](https://aka.ms/azure-jdks)

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре.

### <a name="security-updates"></a>Обновления для системы безопасности

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Дальнейшие действия

Посетите центр [Azure для разработчиков Java](/java/azure/), чтобы найти краткие руководства Azure, руководства и справочную документацию по Java.

Ответы на общие вопросы об использовании службы приложений для Linux, не относящиеся к разработке для Java, можно найти в разделе [вопросов и ответов о службе приложений для Linux](app-service-linux-faq.md).
