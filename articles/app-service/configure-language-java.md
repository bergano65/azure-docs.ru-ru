---
title: Настройка приложений Windows Java
description: Узнайте, как настроить Java-приложения для работы в экземплярах Windows VM в службе приложений Azure. В этой статье показаны наиболее распространенные задачи настройки.
keywords: лазурный сервис приложений, веб-приложение, окна, осс, Java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2b21061e8a939b91c637ef05bbe6375c0b3f82e8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383979"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Настройте приложение Windows Java для службы приложений Azure

Служба приложений Azure позволяет Java-разработчикам быстро создавать, развертывать и масштабировать свои веб-приложения Tomcat на полностью управляемом сервисе на базе Windows. Возможно развертывание приложений с подключаемыми модулями Maven из командной строки или в редакторах, например Visual Studio Code, Eclipse или IntelliJ.

В этом руководстве содержатся ключевые концепции и инструкции для Java-разработчиков, использующих в App Service. Если вы никогда не пользовались службой приложений Azure, сначала следует прочитать [быстрый запуск Java.](app-service-web-get-started-java.md) Общие вопросы об использовании Службы приложений, не специфичные для разработки Java, даны ответы в [часто задаваемых вопросов службы Windows.](faq-configuration-and-management.md)

## <a name="deploying-your-app"></a>Развертывание приложения

Для развертывания файлов .war можно использовать [Azure Web App Plugin для Maven.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) Развертывание с популярными IDEs также поддерживается [С помощью Azure Toolkit для IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) или [Azure Toolkit для Eclipse.](/java/azure/eclipse/azure-toolkit-for-eclipse)

В противном случае метод развертывания будет зависеть от типа архива:

- Чтобы развернуть файлы WAR в Tomcat, используйте конечную точку `/api/wardeploy/` для публикации файла архива. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Для развертывания файлов .jar на Java `/api/zipdeploy/` SE используйте конечную точку сайта Kudu. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Не развертывайте свой .war с помощью FTP. Средство FTP предназначено для передачи сценариев запуска, зависимостей или других файлов среды выполнения. Оно не является оптимальным решением для развертывания веб-приложений.

## <a name="logging-and-debugging-apps"></a>Ведение журнала и отладка приложений

Отчеты о производительности, визуализация трафика и проверка работоспособности доступны на портале Azure для каждого приложения. Для получения дополнительной информации смотрите [обзор диагностики службы приложений Azure](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Использование бортового регистратора

Все время выполнения Java в Службе приложений с помощью JVMs Azul поставляются с помощью бортового самописца Зулу. Это можно использовать для записи событий уровня JVM, системы и Java для мониторинга поведения и устранения неполадок в Java-приложениях.

Для своевременной записи вам понадобится PID (Process ID) приложения Java. Чтобы найти PID, откройте браузер на сайте SCM вашего веб-приложения на https:/<ваш сайт-имя>.scm.azurewebsites.net/ProcessExplorer/. На этой странице показаны запущенные процессы в веб-приложении. Найдите процесс под названием "java" в таблице и скопируйте соответствующий PID (Идентификатор процесса).

Затем откройте **консоль Debug** в верхней панели инструментов сайта SCM и запустите следующую команду. Замените `<pid>` идентификатор процесса, который вы скопировали ранее. Эта команда запустит 30-секундную запись профиля вашего Java-приложения и создаст файл, названный `timed_recording_example.jfr` в каталоге. `D:\home`

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Для получения дополнительной информации, пожалуйста, смотрите [ссылку командования Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Анализ `.jfr` файлов

Используйте [FTPS,](deploy-ftp.md) чтобы загрузить файл JFR на локальную машину. Чтобы проанализировать файл JFR, загрузите и установите [зулу Сулу Управления полетами](https://www.azul.com/products/zulu-mission-control/). Для получения инструкций по управлению [Azul documentation](https://docs.azul.com/zmc/) полетами Зулу см. [installation instructions](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Для получения дополнительной информации смотрите [журналы Stream в облачной оболочке](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Ведение журнала приложений

Включите [ведение журнала приложений](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) с помощью портала Azure или [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config), чтобы настроить службу приложений для записи выходных данных стандартной консоли приложения и потоков ошибок стандартной консоли в локальную файловую систему или хранилище BLOB-объектов Azure. Запись журналов в локальную файловую систему экземпляра службы приложений отключается через 12 часов после настройки ведения журнала. Если необходимо более длительное хранение, настройте приложение для записи выходных данных в контейнер больших двоичных объектов. Ваши журналы приложений Java и Tomcat можно найти в *каталоге /LogFiles/Application/.*

Если приложение использует [Logback](https://logback.qos.ch/) или [Log4j](https://logging.apache.org/log4j) для трассировки, то эти данные трассировки можно передать в Azure Application Insights для просмотра, выполнив инструкции по настройке платформы ведения журнала в разделе [Просмотр журналов трассировки Java в Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Настройка

Служба приложений Azure поддерживает настройку и настройку коробки через портал Azure и CLI. Просмотрите следующие статьи для конфигурации веб-приложений, не связанных с Java:

- [Настройка параметров приложения](configure-common.md#configure-app-settings)
- [Настройка личного домена](app-service-web-tutorial-custom-domain.md)
- [Настройка SSL-привязок](configure-ssl-bindings.md)
- [Добавление CDN](../cdn/cdn-add-to-web-app.md)
- [Настройка сайта Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Настройка параметров среды выполнения Java

Чтобы установить выделенную память или другие параметры выполнения `JAVA_OPTS` JVM, создайте [настройку приложения](configure-common.md#configure-app-settings) с именем опций. Служба приложения передает этот параметр как переменную среды в время выполнения Java, когда она начинается.

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

### <a name="pre-compile-jsp-files"></a>Предварительное составление файлов JSP

Чтобы повысить производительность приложений Tomcat, вы можете компилировать файлы JSP перед развертыванием в Службе приложений. Вы можете использовать [плагин Maven,](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) предоставленный Apache Sling, или с помощью этого [файла сборки ant.](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)

## <a name="secure-applications"></a>Защита приложений

Java-приложения, работающие в Службе приложений, имеют тот же набор [рекомендаций по безопасности,](/azure/security/security-paas-applications-using-app-services) что и другие приложения.

### <a name="authenticate-users-easy-auth"></a>Аутентификации пользователей (Easy Auth)

Настройка аутентификации приложений на портале Azure с опцией **аутентификации и авторизации.** Вы можете включить аутентификацию с помощью Azure Active Directory или имен для входа в социальные сети, таких как Facebook, Google или GitHub. На портале Azure можно настроить только один поставщик аутентификации. Дополнительные сведения приведены в разделе [Настройка приложения службы приложений для использования входа с помощью Azure Active Directory](configure-authentication-provider-aad.md) и связанных статьях о других поставщиках удостоверений. Если необходимо включить несколько поставщиков входа, следуйте инструкциям в статье [Настройка проверки подлинности и авторизации в службе приложений Azure](app-service-authentication-how-to.md).

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

### <a name="configure-tlsssl"></a>Настройка TLS/SSL

Следуйте инструкциям в [Secure пользовательское имя DNS с привязкой SSL в Azure App Service,](configure-ssl-bindings.md) чтобы загрузить существующий сертификат SSL и привязать его к доменному имени вашего приложения. По умолчанию приложение по-прежнему будет разрешать HTTP-подключения. Выполните соответствующие инструкции в этом руководстве, чтобы принудительно включить SSL и TLS.

### <a name="use-keyvault-references"></a>Использование ссылок KeyVault

[Azure KeyVault](../key-vault/key-vault-overview.md) предоставляет централизованное секретное управление политиками доступа и историей аудита. Вы можете хранить секреты (например, пароли или строки соединения) в KeyVault и получать доступ к этим секретам в приложении через переменные среды.

Во-первых, следуйте инструкциям по [предоставлению вашему приложению доступа к Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) и [внесению ссылки на ваш секрет в настройке приложения.](app-service-key-vault-references.md#reference-syntax) Можно проверить, что ссылка разрешает секрет, печатая переменную среды при удаленном доступе к терминалу Службы приложения.

Чтобы привить эти секреты в файл конфигурации Spring`${MY_ENV_VAR}`или Tomcat, используйте синтаксис с переменной впрыски среды (). Для файлов конфигурации Spring, пожалуйста, ознакомьтесь с этой документацией по [экстернализованным конфигурациям.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)


## <a name="configure-apm-platforms"></a>Настройка платформ APM

В этом разделе показано, как подключить Java-приложения, развернутые в Службе приложений Azure на Linux, с платформами мониторинга производительности приложений NewRelic и AppDynamics (APM).

### <a name="configure-new-relic"></a>Настройка New Relic

1. Создание новой учетной записи Relic в [NewRelic.com](https://newrelic.com/signup)
2. Скачать Java-агент от NewRelic, он будет иметь имя файла похож на *newrelic-java-x.x.x.zip*.
3. Скопируйте ключ лицензии. Он понадобиться позже для настройки агента.
4. Используйте [консоль Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) для создания нового каталога */дома/сайта/wwwroot/apm*.
5. Загрузите распакованные файлы агента New Relic Java в каталог *под /home/site/wwwroot/apm*. Файлы для вашего агента должны быть в */дома /сайт / wwwroot/apm/newrelic*.
6. Измените файл YAML по адресу */home/site/wwwroot/apm/newrelic/newrelic.yml* и замените стоимость лицензии заполнителя своим собственным ключом лицензии.
7. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если приложение использует **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Настройка AppDynamics

1. Создайте учетную запись AppDynamics на сайте [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Скачать Java-агент с веб-сайта AppDynamics, имя файла будет похоже на *Имя AppServerAgent-x.x.x.xxxxx.zip*
3. Используйте [консоль Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) для создания нового каталога */дома/сайта/wwwroot/apm*.
4. Загрузите файлы Java-агента в каталог *под /home/site/wwwroot/apm*. Файлы для вашего агента должны быть в */дома/сайте/wwwroot/apm/appdynamics.*
5. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если вы используете **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.

>  Если у вас уже есть переменная среды `JAVA_OPTS` или `CATALINA_OPTS`, добавьте параметр `-javaagent:/...` в конец текущего значения.

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

#### <a name="finalize-configuration"></a>Завершение конфигурации

Наконец, мы поместим драйверы JARs в классto-маршрут Tomcat и перезакончить службу приложений. Убедитесь, что файлы драйверов JDBC доступны для класса Tomcat, разместив их в *каталоге /home/tomcat/lib.* (Создайте этот каталог, если он еще не существует.) Чтобы загрузить эти файлы в экземпляр Службы приложения, выполните следующие действия:

1. В [облачной оболочке](https://shell.azure.com)установите расширение webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Запустите следующую команду CLI, чтобы создать туннель SSH из локальной системы в Службу приложений:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Подключитесь к локальному туннелирующему порту с клиентом SFTP и загрузите файлы в *папку /home/tomcat/lib.*

Кроме того, драйвер JDBC можно отправить с помощью FTP-клиента. Чтобы получить учетные данные FTP, следуйте этим [инструкциям](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Настройка Томката

Чтобы отредкировать `server.xml` файлы Tomcat или другие конфигурации, сначала обратите внимание на основную версию Tomcat на портале.

1. Найдите домашний каталог Tomcat для `env` вашей версии, запустив команду. Поиск переменной среды, `AZURE_TOMCAT`которая начинается с и соответствует вашей основной версии. Например, `AZURE_TOMCAT85_HOME` указываетна на каталог Tomcat для Tomcat 8.5.
1. После того как вы определили домашний каталог Tomcat для `D:\home`вашей версии, скопируйте каталог конфигурации. Например, `AZURE_TOMCAT85_HOME` если бы `D:\Program Files (x86)\apache-tomcat-8.5.37`значение было, новый путь скопированный каталог был `D:\home\apache-tomcat-8.5.37`бы .

Наконец, перезапустите службу приложений. Развертывание должно идти `D:\home\site\wwwroot\webapps` так же, как и раньше.

## <a name="configure-java-se"></a>Настройка Java SE

При запуске . Приложение JAR на Java SE на Windows `server.port` передается как опция командной строки с запуском приложения. Вы можете вручную решить http порт `HTTP_PLATFORM_PORT`из переменной среды, . Значение этой переменной среды будет http порт приложение должно слушать. 

## <a name="java-runtime-statement-of-support"></a>Заявление о поддержке среды выполнения Java

### <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Azure поддерживает пакет Java Development Kit (JDK) [Zulu](https://www.azul.com/downloads/azure-only/zulu/), предоставляемый компанией [Azul Systems](https://www.azul.com/).

Основные обновления версий будут предоставляться с помощью новых вариантов выполнения в Azure App Service для Windows. Пользователи, устанавливающие более новые версии Java посредством настройки развернутой службы приложений, несут ответственность за тестирование выбранных обновлений для основного номера версии и их соответствие своим потребностям.

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре. Для получения дополнительной информации о Java на Azure, пожалуйста, смотрите [этот документ поддержки](https://docs.microsoft.com/azure/java/jdk/).

### <a name="security-updates"></a>Обновления для системы безопасности

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

Tomcat 8.0 достиг [конца жизни (EOL) по состоянию на 30 сентября 2018 года.](https://tomcat.apache.org/tomcat-80-eol.html) Несмотря на то, что время выполнения по-прежнему является пригодным для выполнения в службе приложений Azure, Azure не будет применять обновления безопасности к Tomcat 8.0. Если это возможно, переносите приложения в Tomcat 8.5 или 9.0. Как Tomcat 8.5, так и 9.0 доступны в службе приложений Azure. Дополнительную информацию можно узнать на [официальном сайте Tomcat.](https://tomcat.apache.org/whichversion.html) 

### <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

### <a name="local-development"></a>Локальная разработка

Разработчики могут скачать выпуск Production Edition пакета Azul Zulu Enterprise JDK для локальной разработки с [сайта загрузки Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Поддержка разработки

Поддержка продуктов для [azul zulu JDK, поддерживаемая Azuр,](https://www.azul.com/downloads/azure-only/zulu/) доступна через корпорацию Майкрософт при разработке для Azure или [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) с [квалифицированным планом поддержки Azure.](https://azure.microsoft.com/support/plans/)

### <a name="runtime-support"></a>Поддержка времени выполнения

Разработчики могут [сообщить о проблеме](/azure/azure-portal/supportability/how-to-create-azure-support-request) с Azul Zulu JDK в службу поддержки Azure при наличии [соответствующего плана поддержки](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Дальнейшие действия

Эта тема содержит заявление Java Runtime о поддержке службы приложений Azure в Windows.

- Чтобы узнать больше о хостинге веб-приложений с Azure App Service [см.](overview.md)
- Для получения информации о Java в разработке Azure см. [Azure для Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
