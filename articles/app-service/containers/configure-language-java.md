---
title: Настройка приложений Java для Linux — служба приложений Azure | Документация Майкрософт
description: Сведения о настройке приложений Java, работающих в службе приложений Azure в Linux.
keywords: служба приложений Azure, веб-приложение, Linux, OSS, Java, Java EE, JEE, Java
services: app-service
author: bmitchell287
manager: barbkess
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 9625870132d088bf1de6df06f05f0cac41a1e7fa
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144231"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Настройка приложения Java для Linux для службы приложений Azure

Служба приложений Azure в Linux позволяет разработчикам быстро создавать, развертывать и масштабировать веб-приложения на основе пакетов Tomcat Java или Java Standard Edition (SE) в полностью управляемой службе под управлением Linux. Возможно развертывание приложений с подключаемыми модулями Maven из командной строки или в редакторах, например Visual Studio Code, Eclipse или IntelliJ.

Это краткое описание содержит основные понятия и инструкции для разработчиков Java, использующих встроенный контейнер Linux в службе приложений. Если вы никогда не использовали службу приложений Azure, сначала выполните инструкции из руководства по [Java](quickstart-java.md) и [Java с помощью PostgreSQL](tutorial-java-enterprise-postgresql-app.md) .

## <a name="deploying-your-app"></a>Развертывание приложения

Вы можете использовать [подключаемый модуль Maven для службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) , чтобы развернуть JAR-и WAR-файлы. Развертывание с популярными IDE также поддерживается с [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) или [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

В противном случае ваш метод развертывания будет зависеть от типа архива:

- Чтобы развернуть файлы WAR в Tomcat, используйте конечную точку `/api/wardeploy/` для публикации файла архива. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Чтобы развернуть файлы JAR в образах Java SE, используйте конечную точку `/api/zipdeploy/` сайта Kudu. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Не развертывайте файлы WAR или JAR с помощью FTP. Средство FTP предназначено для передачи сценариев запуска, зависимостей или других файлов среды выполнения. Оно не является оптимальным решением для развертывания веб-приложений.

## <a name="logging-and-debugging-apps"></a>Ведение журнала и отладка приложений

Отчеты о производительности, визуализация трафика и проверка работоспособности доступны на портале Azure для каждого приложения. Дополнительные сведения см. в статье [Обзор диагностики службы приложений Azure](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Доступ к консоли SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Дополнительные сведения см. [в статье потоковая передача журналов в Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Ведение журнала приложений

Включите [ведение журнала приложений](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enable-application-logging-windows) с помощью портала Azure или [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config), чтобы настроить службу приложений для записи выходных данных стандартной консоли приложения и потоков ошибок стандартной консоли в локальную файловую систему или хранилище BLOB-объектов Azure. Запись журналов в локальную файловую систему экземпляра службы приложений отключается через 12 часов после настройки ведения журнала. Если необходимо более длительное хранение, настройте приложение для записи выходных данных в контейнер больших двоичных объектов. Журналы приложений Java и Tomcat можно найти в каталоге */Хоме/логфилес/аппликатион/* .

Если приложение использует [Logback](https://logback.qos.ch/) или [Log4j](https://logging.apache.org/log4j) для трассировки, то эти данные трассировки можно передать в Azure Application Insights для просмотра, выполнив инструкции по настройке платформы ведения журнала в разделе [Просмотр журналов трассировки Java в Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Средства устранения неполадок

Встроенные образы Java основаны на операционной системе [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Используйте диспетчер пакетов `apk`, чтобы установить любые средства или команды для устранения неполадок.

### <a name="flight-recorder"></a>Черный ящик

Все образы Linux Java в службе приложений имеют установленный Zulu "черного ящика", чтобы вы могли легко подключаться к ВИРТУАЛЬНОЙ машины Java и запускать запись профилировщика или создавать дамп кучи.

#### <a name="timed-recording"></a>Время записи

Чтобы приступить к работе, подключитесь к службе приложений по протоколу SSH и выполните команду `jcmd`, чтобы просмотреть список всех запущенных процессов Java. Кроме жкмд, вы должны увидеть, что приложение Java выполняется с ИДЕНТИФИКАТОРом процесса (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Выполните приведенную ниже команду, чтобы запустить 30-секундную запись ВИРТУАЛЬНОЙ машины Java. Это приведет к профилированию ВИРТУАЛЬНОЙ машины Java и созданию файла ЖФР с именем *jfr_example. ЖФР* в домашнем каталоге. (Замените 116 идентификатором PID приложения Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

В течение 30-секундного интервала можно проверить запись, выполнив `jcmd 116 JFR.check`. Будут показаны все записи для данного процесса Java.

#### <a name="continuous-recording"></a>Непрерывная запись

Zulu «черного ящика» можно использовать для непрерывного профилирования приложения Java с минимальным влиянием на производительность среды выполнения ([источник](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Для этого выполните следующую команду Azure CLI, чтобы создать параметр приложения с именем JAVA_OPTS с требуемой конфигурацией. Содержимое параметра JAVA_OPTS приложения передается команде `java` при запуске приложения.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

После начала записи вы можете в любое время сохранить дамп текущих данных, используя команду `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Дополнительные сведения см. в [справочнике по командам жкмд](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Анализ записей

Используйте [FTPS](../deploy-ftp.md) для загрузки файла ЖФР на локальный компьютер. Чтобы проанализировать файл ЖФР, скачайте и установите [элемент управления Zulu миссии](https://www.azul.com/products/zulu-mission-control/). Инструкции по управлению Zulu миссии см. в [документации по Azul](https://docs.azul.com/zmc/) и [инструкциям по установке](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Настройка

Служба приложений Azure для Linux поддерживает настройку и настройку с помощью портал Azure и интерфейса командной строки. Ознакомьтесь со следующими статьями для конфигурации веб-приложения, не относящегося к Java:

- [Настройка параметров приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Настройка личного домена](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Настройка SSL-привязок](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Добавление CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Настройка сайта KUDU](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Настройка параметров среды выполнения Java

Чтобы задать выделенную память или другие параметры среды выполнения ВИРТУАЛЬНОЙ машины Java в средах Tomcat и Java SE, создайте [параметр приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) с именем `JAVA_OPTS` с параметрами. При запуске служба приложений для Linux передает этот параметр в качестве переменной среды в среду выполнения Java.

На портале Azure в разделе **Параметры приложения** для веб-приложения создайте параметр приложения `JAVA_OPTS`, включающий в себя дополнительные параметры, такие как `-Xms512m -Xmx1204m`.

Чтобы настроить параметр приложения из подключаемого модуля Maven, добавьте теги параметров и значений в разделе подключаемый модуль Azure. В следующем примере задается заданный минимальный и максимальный размер кучи Java:

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
- Экземпляры B2 и S2: `-Xms3072m -Xmx3072m`
- Экземпляры B3 и S3: `-Xms6144m -Xmx6144m`

При настройке параметров кучи приложения просмотрите сведения о плане службы приложений и примите во внимание, что наличие нескольких приложений и одного слота развертывания требует оптимального выделения памяти.

Если вы развертываете JAR-приложение, оно должно называться *app. jar* , чтобы встроенный образ мог правильно опознать ваше приложение. (Подключаемый модуль Maven выполняет это переименование автоматически.) Если вы не хотите переименовывать JAR-файл в *app. jar*, вы можете отправить сценарий оболочки с помощью команды, чтобы запустить JAR. Затем вставьте полный путь к этому скрипту в текстовом поле [файл запуска](app-service-linux-faq.md#built-in-images) в разделе конфигурации на портале. Сценарий запуска не запускается из каталога, в который он помещен. Поэтому всегда используйте абсолютные пути для ссылок на файлы в скрипте запуска (например, `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>Настроить время ожидания запуска

Если приложение Java особенно велико, следует увеличить предельное время запуска. Для этого создайте параметр приложения `WEBSITES_CONTAINER_START_TIME_LIMIT` и задайте для него время ожидания в секундах, по истечении которого служба приложений должна ждать. Максимальное значение — `1800` секунд.

### <a name="pre-compile-jsp-files"></a>Предварительная компиляция файлов JSP

Чтобы повысить производительность приложений Tomcat, можно скомпилировать файлы JSP перед развертыванием в службе приложений. Вы можете использовать [подключаемый модуль Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) , предоставляемый Apache слинг, или использовать этот [Ant файл сборки](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Защита приложений

Для приложений Java, работающих в службе приложений для Linux, предлагается тот же набор [рекомендаций по обеспечению безопасности](/azure/security/security-paas-applications-using-app-services), что и для других приложений.

### <a name="authenticate-users-easy-auth"></a>Проверка подлинности пользователей (простая проверка подлинности)

Настройте проверку подлинности приложения в портал Azure с помощью параметра **Проверка подлинности и авторизация** . Вы можете включить аутентификацию с помощью Azure Active Directory или имен для входа в социальные сети, таких как Facebook, Google или GitHub. На портале Azure можно настроить только один поставщик аутентификации. Дополнительные сведения приведены в разделе [Настройка приложения службы приложений для использования входа с помощью Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) и связанных статьях о других поставщиках удостоверений. Если необходимо включить несколько поставщиков входа, следуйте инструкциям в статье [Настройка проверки подлинности и авторизации в службе приложений Azure](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

#### <a name="tomcat-and-wildfly"></a>Tomcat и Вилдфли

Приложение Tomcat или Вилдфли может получить доступ к утверждениям пользователя непосредственно из сервлета путем приведения объекта Principal к объекту Map. Объект Map будет сопоставлять каждый тип утверждения с коллекцией утверждений для этого типа. В приведенном ниже коде `request` является экземпляром `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Теперь можно проверить объект `Map` для любого конкретного утверждения. Например, следующий фрагмент кода выполняет перебор всех типов заявок и выводит содержимое каждой коллекции.

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

Чтобы подписать пользователей, используйте `/.auth/ext/logout` путь. Чтобы выполнить другие действия, см. документацию по [использованию проверки подлинности и авторизации службы приложений](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Существует также официальная документация по [интерфейсу Tomcat хттпсервлетрекуест](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) и его методам. В зависимости от конфигурации службы приложений также сохраняются следующие методы сервлета:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Чтобы отключить эту функцию, создайте параметр приложения с именем `WEBSITE_AUTH_SKIP_PRINCIPAL` со значением `1`. Чтобы отключить все фильтры сервлета, добавленные службой приложений, создайте параметр с именем `WEBSITE_SKIP_FILTERS` со значением `1`.

#### <a name="spring-boot"></a>Spring Boot

Разработчики для Spring Boot могут использовать [краткое руководство по использованию Spring Boot и Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable), чтобы защитить приложения с помощью привычных заметок и интерфейсов API Spring Security. Не забудьте увеличить максимальный размер заголовка в файле *приложения. Properties* . Мы рекомендуем использовать значение `16384`.

### <a name="configure-tlsssl"></a>Настройка TLS/SSL

Следуйте инструкциям в разделе [Защита настраиваемого DNS-имени с помощью привязки SSL в службе приложений Azure](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) для отправки существующего SSL-сертификата и привязки его к доменному имени приложения. По умолчанию приложение по-прежнему будет разрешать HTTP-подключения. Выполните соответствующие инструкции в этом руководстве, чтобы принудительно включить SSL и TLS.

### <a name="use-keyvault-references"></a>Использование ссылок KeyVault

[Azure KeyVault](../../key-vault/key-vault-overview.md) обеспечивает централизованное управление секретами с помощью политик доступа и журнала аудита. Вы можете хранить секреты (например, пароли или строки подключения) в KeyVault и обращаться к этим секретам в приложении с помощью переменных среды.

Сначала следуйте инструкциям, [чтобы предоставить приложению доступ к Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) и [сделать ссылку KeyVault на секрет в параметре приложения](../app-service-key-vault-references.md#reference-syntax). Можно проверить, что ссылка разрешается в секрет, выполнив печать переменной среды во время удаленного доступа к терминалу службы приложений.

Чтобы внедрить эти секреты в файл конфигурации весны или Tomcat, используйте синтаксис внедрения переменных среды (`${MY_ENV_VAR}`). Дополнительные сведения о файлах конфигурации пружины см. в этой документации по [внешним конфигурациям](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Использование хранилища ключей Java

По умолчанию все общедоступные или частные сертификаты, [Отправленные в службу приложений Linux](../configure-ssl-certificate.md) , будут загружены в хранилище ключей Java при запуске контейнера. Это означает, что отправленные сертификаты будут доступны в контексте подключения при создании исходящих TLS подключений. После отправки сертификата необходимо перезапустить службу приложений, чтобы она загрузилась в хранилище ключей Java.

Вы можете взаимодействовать или отлаживать инструмент для работы с ключами Java, [открыв SSH-подключение](app-service-linux-ssh-support.md) к службе приложений и выполнив команду `keytool`. Список команд см. в [документации по основным средствам](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) . Сертификаты хранятся в расположении файлов хранилища ключей Java по умолчанию, `$JAVA_HOME/jre/lib/security/cacerts`.

Для шифрования подключения JDBC может потребоваться дополнительная настройка. Обратитесь к документации по выбранному драйверу JDBC.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)

#### <a name="manually-initialize-and-load-the-key-store"></a>Инициализация и загрузка хранилища ключей вручную

Вы можете инициализировать хранилище ключей и добавить сертификаты вручную. Создайте параметр приложения `SKIP_JAVA_KEYSTORE_LOAD`со значением `1`, чтобы отключить автоматическую загрузку сертификатов в хранилище ключей службой приложений. Все общедоступные сертификаты, отправленные в службу приложений через портал Azure, хранятся в `/var/ssl/certs/`. Частные сертификаты хранятся в разделе `/var/ssl/private/`.

Дополнительные сведения об API хранилища ключей см. в [официальной документации](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Настройка платформ APM

В этом разделе показано, как подключить приложения Java, развернутые в службе приложений Azure на платформе Linux, с помощью платформ мониторинга производительности приложений NewRelic и AppDynamics (APM).

### <a name="configure-new-relic"></a>Настройка New Relic

1. Создайте учетную запись NewRelic на сайте [NewRelic.com](https://newrelic.com/signup)
2. Скачайте агент Java из NewRelic, он будет иметь имя файла, аналогичное *неврелик-Жава-КС. x. x. zip*.
3. Скопируйте ключ лицензии. Он понадобиться позже для настройки агента.
4. Подключитесь к [экземпляру службы приложений](app-service-linux-ssh-support.md) по протоколу SSH и создайте новый каталог */Хоме/Сите/ввврут/АПМ*.
5. Передайте распакованные файлы агента Java NewRelic в каталог в папке */Хоме/Сите/ввврут/АПМ*. Файлы для агента должны быть в */Хоме/Сите/ввврут/АПМ/неврелик*.
6. Измените файл YAML по адресу */Хоме/Сите/ввврут/АПМ/неврелик/неврелик.ИМЛ* и замените значение лицензии заполнителя на свой собственный лицензионный ключ.
7. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если приложение использует **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Если вы используете **вилдфли**, ознакомьтесь с новой документацией Relic [здесь](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) , чтобы получить рекомендации по установке агента Java и конфигурации JBoss.

### <a name="configure-appdynamics"></a>Настройка AppDynamics

1. Создайте учетную запись AppDynamics на сайте [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Скачайте агент Java с веб-сайта AppDynamics, имя файла будет похоже на *аппсерверажент-КС. x. x. xxxxx. zip* .
3. Подключитесь к [экземпляру службы приложений](app-service-linux-ssh-support.md) по протоколу SSH и создайте новый каталог */Хоме/Сите/ввврут/АПМ*.
4. Отправьте файлы агента Java в каталог в разделе */Хоме/Сите/ввврут/АПМ*. Файлы для агента должны быть в */Хоме/Сите/ввврут/АПМ/аппдинамикс*.
5. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если вы используете **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.
    - Если вы используете **вилдфли**, см. документацию AppDynamics [здесь](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) , чтобы получить рекомендации по установке агента Java и конфигурации JBoss.

>  Если у вас уже есть переменная среды `JAVA_OPTS` или `CATALINA_OPTS`, добавьте параметр `-javaagent:/...` в конец текущего значения.

## <a name="configure-jar-applications"></a>Настройка JAR-приложений

### <a name="starting-jar-apps"></a>Запуск JAR-приложений

По умолчанию служба приложений ждет, что приложение JAR будет называться *app. jar*. Если оно имеет это имя, оно будет запущено автоматически. Для пользователей Maven можно задать имя JAR, включив `<finalName>app</finalName>` в раздел `<build>` файла *POM. XML*. [Вы можете сделать то же самое в Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) , задав свойство `archiveFileName`.

Если вы хотите использовать другое имя для JAR-файла, необходимо также указать [команду запуска](app-service-linux-faq.md#built-in-images) , которая ВЫПОЛНЯЕТ файл JAR. Пример: `java -jar my-jar-app.jar`. Можно задать значение для команды запуска на портале, в разделе Конфигурация > Общие параметры или с параметром приложения с именем `STARTUP_COMMAND`.

### <a name="server-port"></a>Порт сервера

Служба приложений Linux направляет входящие запросы на порт 80, поэтому ваше приложение должно также прослушивать порт 80. Это можно сделать в конфигурации приложения (например, в файле "Весна" *приложения* ) или в команде запуска (например, `java -jar spring-app.jar --server.port=80`). См. следующую документацию по общим платформам Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [спаркжава](http://sparkjava.com/documentation#embedded-web-server)
- [микронаут](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Воспроизвести платформу](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [верткс](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [куаркус](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Источники данных

### <a name="tomcat"></a>Tomcat

Эти инструкции применимы ко всем подключениям к базе данных. Необходимо будет заменить значения заполнителей на имя класса драйвера и JAR-файл выбранной базы данных. Ниже приведена таблица с именами классов и ссылками для скачивания драйверов для распространенных баз данных.

| База данных   | Имя класса драйвера                             | Драйвер JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Загрузить](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Скачать](https://dev.mysql.com/downloads/connector/j/) (выберите "Platform Independent" (Независимо от платформы)) |
| SQL Server; | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Загрузить](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Чтобы настроить Tomcat для использования Java Database Connectivity (JDBC) или API сохраняемости Java (JPA), сначала настройте переменную среды `CATALINA_OPTS`, которая считывается в Tomcat при запуске. Задайте эти значения с помощью параметра приложения в [подключаемом модуле Maven для службы приложений](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Или задайте переменные среды на странице Параметры **конфигурации** > **приложения** в портал Azure.

Затем определите, должен ли источник данных быть доступным для одного приложения или для всех приложений, работающих в сервлете Tomcat.

#### <a name="application-level-data-sources"></a>Источники данных на уровне приложения

1. Создайте файл *context. XML* в каталоге *META-INF или* проекте. Создайте *файл META-INF или* каталог, если он не существует.

2. В *context. XML*добавьте элемент `Context`, чтобы связать источник данных с адресом JNDI. Замените заполнитель `driverClassName` именем класса драйвера из приведенной выше таблицы.

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

3. Обновите *файл Web. XML* приложения, чтобы использовать источник данных в приложении.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Общие ресурсы уровня сервера

Для добавления общего источника данных на уровне сервера потребуется изменить файл Server. XML Tomcat. Сначала отправьте [сценарий запуска](app-service-linux-faq.md#built-in-images) и задайте путь к скрипту в параметре запуска **Configuration** > **Startup**. Скрипт запуска можно загрузить с помощью [FTP](../deploy-ftp.md).

Сценарий запуска [преобразует XSL-преобразование](https://www.w3schools.com/xml/xsl_intro.asp) в файл Server. XML и выводит полученный XML-файл в `/usr/local/tomcat/conf/server.xml`. Сценарий запуска должен установить либксслт через apk. Файл XSL и скрипт запуска можно отправить через FTP. Ниже приведен пример скрипта запуска.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /usr/local/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /home/tomcat/conf/server.xml
```

Ниже приведен пример XSL-файла. В примере XSL-файла в Tomcat Server. XML добавляется новый узел соединителя.

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

Наконец, поместите драйвер JAR в путь к классам Tomcat и перезапустите службу приложений.

1. Убедитесь, что файлы драйвера JDBC доступны для Tomcat класслоадер, поместив их в каталог */Хоме/томкат/либ* . (Создайте этот каталог, если он еще не существует.) Чтобы передать эти файлы в экземпляр службы приложений, выполните следующие действия.

    1. В [Cloud Shell](https://shell.azure.com)установите расширение webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Выполните следующую команду CLI, чтобы создать туннель SSH из локальной системы в службу приложений:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Подключитесь к локальному порту туннелирования с помощью клиента SFTP и отправьте файлы в папку */Хоме/томкат/либ* .

    Кроме того, драйвер JDBC можно отправить с помощью FTP-клиента. Чтобы получить учетные данные FTP, следуйте этим [инструкциям](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Если вы создали источник данных на уровне сервера, перезапустите приложение Linux службы приложений. Tomcat сбросит `CATALINA_BASE` до значения `/home/tomcat` и будет использовать обновленную конфигурацию.

### <a name="spring-boot"></a>Spring Boot

Чтобы подключиться к источникам данных в приложениях с пружинной загрузкой, мы рекомендуем создать строки подключения и внедрить их в файл *приложения. Properties* .

1. В разделе "Конфигурация" страницы службы приложений задайте имя строки, вставьте строку подключения JDBC в поле "значение" и присвойте типу значение "Custom" (пользовательский). При необходимости можно задать эту строку подключения в качестве параметра слота.

    Эта строка подключения доступна для нашего приложения в виде переменной среды с именем `CUSTOMCONNSTR_<your-string-name>`. Например, строка подключения, созданная выше, будет называться `CUSTOMCONNSTR_exampledb`.

2. В файле *Application. Properties* сослаться на эту строку подключения с помощью имени переменной среды. В нашем примере мы будем использовать следующее.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Дополнительные сведения по этому вопросу см. в [документации по доступу к данным](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) и [внешним конфигурациям](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="configure-java-ee-wildfly"></a>Настройка Java EE (Вилдфли)

> [!NOTE]
> Java Enterprise Edition в службе приложений Linux в настоящее время находится на этапе предварительной версии. Этот стек **не** рекомендуется для работы в рабочей среде.

Служба приложений Azure в Linux позволяет разработчикам Java создавать, развертывать и масштабировать приложения Java Enterprise (Java EE) в полностью управляемой службе на основе Linux.  Базовая среда выполнения Java Enterprise — это сервер приложений [вилдфли](https://wildfly.org/) с открытым исходным кодом.

Этот раздел содержит следующие подразделы:

- [Масштабирование с помощью службы приложений](#scale-with-app-service)
- [Настройка конфигурации сервера приложений](#customize-application-server-configuration)
- [Установка модулей и зависимостей](#install-modules-and-dependencies)
- [Настройка источников данных](#configure-data-sources)
- [Включение поставщиков обмена сообщениями](#enable-messaging-providers)

### <a name="scale-with-app-service"></a>Масштабирование с помощью Службы приложений

Сервер приложений WildFly работает в Службе приложений на платформе Linux в автономном режиме, а не в конфигурации домена. При масштабировании плана службы приложений каждый экземпляр WildFly настраивается как отдельный сервер.

Вы можете выполнять вертикальное или горизонтальное масштабирование своего приложения, придерживаясь [правил масштабирования](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) и [увеличивая число экземпляров](../manage-scale-up.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Настройка параметров сервера приложений

Экземпляры веб-приложения не имеют состояния, поэтому каждый новый экземпляр должен быть настроен при запуске для поддержки конфигурации Вилдфли, необходимой для приложения.
Вы можете написать скрипт запуска Bash для вызова интерфейса командной строки WildFly и выполнения таких действий:

- настройка источников данных;
- настройка поставщиков службы обмена сообщениями;
- Добавьте другие модули и зависимости в конфигурацию сервера Вилдфли.

Сценарий выполняется, когда Вилдфли работает, но перед запуском приложения. Скрипт должен использовать интерфейс [командной строки JBoss](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) , вызываемый из */ОПТ/жбосс/вилдфли/бин/жбосс-кли.ш* , чтобы настроить сервер приложений с любой конфигурацией или изменениями, необходимыми после запуска сервера.

Не используйте интерактивный режим CLI для настройки Вилдфли. Вместо этого можно передать скрипт с командами в интерфейс командной строки JBoss с помощью команды `--file`, например:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Используйте FTP для передачи скрипта запуска в расположение в экземпляре службы приложений в каталоге */Home* , например */Хоме/Сите/деплойментс/тулс*. Дополнительные сведения см. в статье [развертывание приложения в службе приложений Azure с помощью FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Задайте в поле **скрипт запуска** в портал Azure расположение сценария оболочки запуска, например */Хоме/Сите/деплойментс/тулс/йоур-стартуп-скрипт.ш*.

Укажите [Параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) в конфигурации приложения, чтобы передать переменные среды для использования в скрипте. В параметрах приложения хранятся строки подключения и другие секретные данные, необходимые для настройки приложения из системы управления версиями.

### <a name="install-modules-and-dependencies"></a>Установка модулей и зависимостей

Чтобы установить модули и их зависимости в подкаталог Вилдфли через интерфейс командной строки JBoss, необходимо создать следующие файлы в своем собственном каталоге. Для некоторых модулей и зависимостей может понадобиться дополнительная настройка (например, JNDI или других API). Следующий список включает минимальный набор параметров для настройки зависимостей, применимый в большинстве случаев.

- [Дескриптор модуля XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Этот XML-файл определяет имя, атрибуты и зависимости вашего модуля. Этот [пример файла module.xml](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) определяет модуль Postgres, зависимость JDBC (файл с расширением .jar) и другие требуемые зависимости модуля.
- Все необходимые зависимости для вашего модуля (файлы с расширением .jar).
- Скрипт с командами интерфейса командной строки JBoss CLI для настройки нового модуля. Этот файл будет содержать команды для выполнения в интерфейсе командной строки JBoss, благодаря которым сервер сможет использовать зависимости. См. [документацию по командам для добавления модулей, источников данных и поставщиков службы обмена сообщениями ](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Скрипт запуска Bash для вызова интерфейса командной строки JBoss и выполнения скрипта из предыдущего шага. Этот файл будет выполняться при перезапуске экземпляра службы приложений или при подготовке новых экземпляров во время горизонтального масштабирования. Этот сценарий запуска позволяет выполнять любые другие настройки приложения, так как команды JBoss передаются в интерфейс командной строки JBoss. Как минимум, этот файл можно использовать как отдельную команду для передачи соответствующего скрипта JBoss в интерфейс командной строки JBoss:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

После получения файлов и содержимого для модуля выполните следующие действия, чтобы добавить модуль на сервер приложений Вилдфли.

1. Используйте FTP для передачи файлов в расположение в экземпляре службы приложений в каталоге */Home* , например */Хоме/Сите/деплойментс/тулс*. Дополнительные сведения см. в статье [развертывание приложения в службе приложений Azure с помощью FTP/S](../deploy-ftp.md).
2. На странице **настройка** > **Общие параметры** портал Azure задайте в поле **сценарий запуска** расположение сценария оболочки запуска, например */Хоме/Сите/деплойментс/тулс/стартуп.ш*.
3. Перезапустите экземпляр службы приложений, нажав кнопку **перезапустить** в разделе **Обзор** портала или с помощью Azure CLI.

### <a name="configure-data-sources"></a>Настройка источников данных

Чтобы настроить Вилдфли/JBoss для доступа к источнику данных, используйте общий процесс, описанный выше в разделе "Установка модулей и зависимостей". В следующем разделе приведены подробные сведения об этом процессе для источников данных PostgreSQL, MySQL и SQL Server.

В этом разделе предполагается, что у вас уже есть приложение, экземпляр службы приложений и экземпляр службы базы данных Azure. Приведенные ниже инструкции относятся к имени службы приложений, ее группе ресурсов и сведениям о подключении к базе данных. Эти сведения можно найти на портал Azure.

Если вы предпочитаете весь процесс с начала работы с примером приложения, см. раздел [учебник. Создание веб-приложения Java EE и postgres в Azure](tutorial-java-enterprise-postgresql-app.md).

Следующие шаги описывают требования к подключению существующей службы приложений и базы данных.

1. Скачайте драйвер JDBC для [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)или [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Распакуйте скачанный архив, чтобы получить JAR-файл драйвера.

2. Создайте файл с именем, например *Module. XML* , и добавьте следующую разметку. Замените заполнитель `<module name>` (включая угловые скобки) `org.postgres` для PostgreSQL, `com.mysql` для MySQL или `com.microsoft` для SQL Server. Замените `<JDBC .jar file path>` именем JAR-файла из предыдущего шага, включая полный путь к расположению, в которое будет размещен файл в экземпляре службы приложений. Это может быть любое расположение в каталоге */Home* .

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Создайте файл с именем, например *датасаурце-коммандс. CLI* , и добавьте следующий код. Замените `<JDBC .jar file path>` значением, использованным на предыдущем шаге. Замените `<module file path>` именем файла и путем службы приложений из предыдущего шага, например */Хоме/модуле.ксмл*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Этот файл запускается сценарием запуска, описанным на следующем шаге. Он устанавливает драйвер JDBC в качестве модуля Вилдфли, создает соответствующий источник данных Вилдфли и перезагружает сервер, чтобы убедиться, что изменения вступят в силу.

4. Создайте файл с именем, например *Startup.sh* , и добавьте следующий код. Замените `<JBoss CLI script>` именем файла, созданного на предыдущем шаге. Не забудьте указать полный путь к расположению, в которое будет размещен файл в экземпляре службы приложений, например */Хоме/датасаурце-коммандс.кли*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Используйте протокол FTP для передачи JAR-файла JDBC, файла XML модуля, скрипта CLI JBoss и скрипта запуска для экземпляра службы приложений. Вставьте эти файлы в расположение, указанное на предыдущих шагах, например */Home*. Дополнительные сведения о FTP см. в статье [развертывание приложения в службе приложений Azure с помощью FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Используйте Azure CLI, чтобы добавить параметры в службу приложений, которые содержат сведения о подключении к базе данных. Замените `<resource group>` и `<webapp name>` значениями, используемыми службой приложений. Замените `<database server name>`, `<database name>`, `<admin name>`и `<admin password>` сведениями о подключении к базе данных. Вы можете получить сведения о службе приложений и базе данных из портал Azure.

    **PostgreSQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    Значения DATABASE_CONNECTION_URL различаются для каждого сервера базы данных и отличаются от значений в портал Azure. Приведенные здесь форматы URL-адресов (и в приведенных выше фрагментах) необходимы для использования Вилдфли:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. В портал Azure перейдите к службе приложений и найдите страницу **Configuration** > **General Settings (общие параметры** ). Задайте в поле **скрипт запуска** имя и расположение сценария запуска, например */Хоме/стартуп.ш*.

При следующем перезапуске службы приложений будет запущен сценарий запуска и выполнены необходимые действия по настройке. Чтобы убедиться, что эта конфигурация выполняется правильно, вы можете получить доступ к службе приложений с помощью SSH, а затем запустить сценарий запуска самостоятельно из командной строки bash. Также можно изучить журналы службы приложений. Дополнительные сведения об этих параметрах см. в разделе [ведение журналов и отладка приложений](#logging-and-debugging-apps).

Далее необходимо обновить конфигурацию Вилдфли для приложения и повторно развернуть его. Выполните следующие действия.

1. Откройте файл *src/Main/Resources/META-INF/сохраняемость. XML* для своего приложения и найдите элемент `<jta-data-source>`. Замените его содержимое, как показано ниже:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Перестройте и повторно разверните приложение, выполнив следующую команду в командной строке Bash:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Перезапустите экземпляр службы приложений, нажав кнопку **перезапустить** в разделе **Обзор** портал Azure или с помощью Azure CLI.

Теперь ваш экземпляр службы приложений настроен для доступа к базе данных.

Дополнительные сведения о настройке подключения к базе данных с помощью Вилдфли см. в разделе [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)или [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="enable-messaging-providers"></a>Включение поставщиков обмена сообщениями

Чтобы включить компоненты, управляемые сообщениями (Message Driven Beans), используя в качестве механизма обмена сообщениями Служебную шину, сделайте следующее:

1. Используйте [библиотеку обмена сообщениями Apache QPId JMS](https://qpid.apache.org/proton/index.html). Включите эту зависимость в файл pom.xml (или другой файл сборки) для приложения.

2. Создайте [ресурсы Служебной шины](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Создайте пространство имен Служебной шины Azure и очередь в пределах этого пространства имен, а также политику общего доступа с возможностью отправки и получения.

3. Передайте ключ политики общего доступа в код путем URL-кодирования первичного ключа политики или [используйте для этого пакет SDK Служебной шины](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Выполните шаги, описанные в разделе "Установка модулей и зависимостей", используя дескриптор модуля XML, зависимости .jar, команды интерфейса командной строки JBoss и скрипт запуска для поставщика JMS. В дополнение к существующим четырем файлам вам также нужно создать XML-файл, определяющий имя JNDI для очереди и раздела JMS. См. дополнительные сведения о соответствующих [файлах конфигурации](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig).

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Использование Redis в качестве кэша сеансов с Tomcat

Вы можете настроить Tomcat для использования внешнего хранилища сеансов, такого как [кэш Azure для Redis](/azure/azure-cache-for-redis/). Это позволяет сохранить состояние сеанса пользователя (например, данные покупательской корзины) при передаче пользователя на другой экземпляр приложения, например при выполнении автоматического масштабирования, перезапуска или отработки отказа.

Чтобы использовать Tomcat с Redis, необходимо настроить приложение для использования реализации [персистентманажер](http://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) . Следующие шаги описывают этот процесс с помощью [диспетчера сеансов сводных данных: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) в качестве примера.

1. Откройте терминал Bash и используйте `export <variable>=<value>`, чтобы задать каждую из следующих переменных среды.

    | Переменная                 | Значение                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Имя группы ресурсов, содержащей экземпляр службы приложений.       |
    | WEBAPP_NAME              | Имя экземпляра службы приложений.                                     |
    | WEBAPP_PLAN_NAME         | Имя плана службы приложений                                          |
    | РЕГИОН                   | Имя региона, в котором размещено приложение.                           |
    | REDIS_CACHE_NAME         | Имя кэша Azure для экземпляра Redis.                           |
    | REDIS_PORT               | Порт SSL, прослушиваемый кэшем Redis.                             |
    | REDIS_PASSWORD           | Первичный ключ доступа для экземпляра.                                  |
    | REDIS_SESSION_KEY_PREFIX | Значение, указанное для определения ключей сеанса, поступивших из приложения. |

    Сведения о имени, порте и ключе доступа можно найти на портал Azure в разделе **Свойства** или **ключи доступа** вашего экземпляра службы.

2. Создайте или обновите файл *src, Main, webapp/META-INF/context. XML* приложения, используя следующее содержимое:

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

    В этом файле указывается и настраивается реализация диспетчера сеансов для приложения. В нем используются переменные среды, заданные на предыдущем шаге, чтобы данные вашей учетной записи не изменялись из исходных файлов.

3. Используйте FTP для передачи JAR-файла диспетчера сеансов в экземпляр службы приложений, поместив его в каталог */Хоме/томкат/либ* . Дополнительные сведения см. в статье [развертывание приложения в службе приложений Azure с помощью FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Отключите [файл cookie сходства сеансов](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) для экземпляра службы приложений. Это можно сделать в портал Azure, перейдя к своему приложению, а затем установив для параметра **Configuration > General Settings (общие параметры) > параметру affinity сходство** значение **Off**. Кроме того, можно использовать следующую команду:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    По умолчанию служба приложений будет использовать файлы cookie сходства сеансов, чтобы обеспечить маршрутизацию клиентских запросов с существующими сеансами к одному и тому же экземпляру приложения. Это поведение по умолчанию не требует настройки, но не может сохранить состояние сеанса пользователя при перезапуске экземпляра приложения или при перенаправлении трафика на другой экземпляр. При [отключении существующей конфигурации сходства экземпляра arr](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) для отключения маршрутизации на основе cookie-файлов сеанса можно разрешить настроенному хранилищу сеансов работу без помех.

5. Перейдите в раздел **свойств** экземпляра службы приложений и найдите **Дополнительные исходящие IP-адреса**. Они представляют все возможные исходящие IP-адреса для вашего приложения. Скопируйте их для использования на следующем шаге.

6. Для каждого IP-адреса создайте правило брандмауэра в кэше Azure для экземпляра Redis. Это можно сделать на портал Azure из раздела **брандмауэр** вашего экземпляра Redis. Укажите уникальное имя для каждого правила и задайте для параметров **начальный IP-адрес** и **конечный IP** -адрес один и тот же IP-адрес.

7. Перейдите к разделу " **Дополнительные параметры** **" в**экземпляре Redis и установите для параметра **Разрешить доступ только через SSL** . Это позволяет вашему экземпляру службы приложений взаимодействовать с кэшем Redis через инфраструктуру Azure.

8. Обновите конфигурацию `azure-webapp-maven-plugin` в файле *POM. XML* вашего приложения, чтобы они ссылались на сведения об учетной записи Redis. В этом файле используются ранее настроенные переменные среды для сохранения данных учетной записи из исходных файлов.

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

9. Перестройте и повторно разверните приложение.

    ```bash
    mvn package
    mvn azure-webapp:deploy
    ```

Теперь приложение будет использовать кэш Redis для управления сеансами.

Пример, который можно использовать для проверки этих инструкций, см. в репозитории с поддержкой [Java-Web-App-On-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) в GitHub.

## <a name="docker-containers"></a>контейнеры Docker;

Чтобы использовать в своих контейнерах поддерживаемый в Azure пакет JDK Zulu, обязательно используйте готовые образы, предоставленные на [странице скачивания Azul Zulu Enterprise для Azure](https://www.azul.com/downloads/azure-only/zulu/), или используйте примеры `Dockerfile` из [репозитория Майкрософт для Java на сайте GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Оператор поддержки

### <a name="runtime-availability"></a>Доступность среды выполнения

Служба приложений для Linux поддерживает две среды выполнения для управляемого размещения веб-приложений Java.

- [Контейнер сервлетов Tomcat](https://tomcat.apache.org/) для запуска приложений, которые упакованы как файлы веб-архива (WAR-файлы). Поддерживаются версии 8.5 и 9.0.
- Среда выполнения Java SE для запуска приложений, которые упакованы как файлы архива Java (JAR-файлы). Поддерживаются версии Java 8 и 11.

### <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Сборка OpenJDK типа Azul Zulu Enterprise — это бесплатный мультиплатформенный, готовый дистрибутив OpenJDK для Azure и Azure Stack, который поддерживается корпорацией Майкрософт и Azul Systems. Они содержат все компоненты для сборки и запуска приложений Java SE. Вы можете установить JDK из [установки Java JDK](https://aka.ms/azure-jdks).

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре.

### <a name="security-updates"></a>обновления для системы безопасности;

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

## <a name="next-steps"></a>Дополнительная информация

Посетите центр [Azure для разработчиков Java](/java/azure/), чтобы найти краткие руководства Azure, руководства и справочную документацию по Java.

Ответы на общие вопросы об использовании службы приложений для Linux, не относящиеся к разработке для Java, можно найти в разделе [вопросов и ответов о службе приложений для Linux](app-service-linux-faq.md).
