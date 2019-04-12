---
title: Руководство разработчика для Java для службы приложений в Linux в Azure | Документация Майкрософт
description: Сведения о настройке приложений Java, работающих в службе приложений Azure в Linux.
keywords: Служба приложений Azure, веб-приложение, Linux, OSS
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: bab6510af98b153ecb61db8fc49b5124aae04598
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500470"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Руководство разработчика для Java для службы приложений в Linux

Служба приложений Azure в Linux позволяет разработчикам быстро создавать, развертывать и масштабировать веб-приложения на основе пакетов Tomcat Java или Java Standard Edition (SE) в полностью управляемой службе под управлением Linux. Возможно развертывание приложений с подключаемыми модулями Maven из командной строки или в редакторах, например Visual Studio Code, Eclipse или IntelliJ.

Это руководство содержит основные понятия и инструкции для разработчиков для Java, использующих службу приложений для Linux. Если вы раньше не использовали Службу приложений Azure для Linux, мы рекомендуем сначала ознакомиться с [кратким руководством по Java](quickstart-java.md). Ответы на общие вопросы об использовании службы приложений для Linux, не относящиеся к разработке для Java, можно найти в разделе [вопросов и ответов о службе приложений для Linux](app-service-linux-faq.md).

## <a name="deploying-your-app"></a>Развертывание приложения

Вы можете использовать модуль Maven для развертывания файлов JAR и WAR. Дополнительные сведения о модуле Maven см. в [Руководстве разработчика для Java для службы приложений в Linux](https://docs.microsoft.com/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable).

Если вы не используете Maven, метод развертывания будет зависеть от типа вашего архива.

- Чтобы развернуть файлы WAR в Tomcat, используйте конечную точку `/api/wardeploy/` для публикации файла архива. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Чтобы развернуть файлы JAR в образах Java SE, используйте конечную точку `/api/zipdeploy/` сайта Kudu. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Не развертывайте файлы WAR или JAR с помощью FTP. Средство FTP предназначено для передачи сценариев запуска, зависимостей или других файлов среды выполнения. Оно не является оптимальным решением для развертывания веб-приложений.

## <a name="logging-and-debugging-apps"></a>Ведение журнала и отладка приложений

Отчеты о производительности, визуализация трафика и проверка работоспособности доступны на портале Azure для каждого приложения. Прочитайте [обзор диагностики с помощью Службы приложений Azure](/azure/app-service/overview-diagnostics), чтобы узнать больше о том, как обращаться к этим средствам диагностики и использовать их.

## <a name="application-performance-monitoring"></a>Мониторинг производительности приложения

Инструкции по настройке New Relic и AppDynamics с приложениями Java, запущенными в Службе приложений Azure на платформе Linux, см. в статье [Практическое руководство. Инструменты мониторинга производительности приложений Java в Службе приложений Azure под управлением Linux](how-to-java-apm-monitoring.md).

### <a name="ssh-console-access"></a>Доступ к консоли SSH

Доступна возможность подключения SSH к среде Linux, на которых выполняется приложение. В разделе [Поддержка SSH для службы приложений Azure в Linux](/azure/app-service/containers/app-service-linux-ssh-support) приведены подробные инструкции по подключению к системе Linux с помощью веб-браузера или локального терминала.

### <a name="streaming-logs"></a>Журналы потоковой передачи.

Для быстрой отладки и устранения неполадок можно осуществлять потоковую передачу журналов в консоль с помощью Azure CLI. Настройте интерфейс командной строки с помощью команды `az webapp log config`, чтобы включить ведение журнала.

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Затем включите потоковую передачу журналов в консоль с помощью команды `az webapp log tail`.

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Дополнительные сведения см. в разделе [Потоковая передача с использованием интерфейса командной строки Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Ведение журнала приложений

Включите [ведение журнала приложений](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) с помощью портала Azure или [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config), чтобы настроить службу приложений для записи выходных данных стандартной консоли приложения и потоков ошибок стандартной консоли в локальную файловую систему или хранилище BLOB-объектов Azure. Запись журналов в локальную файловую систему экземпляра службы приложений отключается через 12 часов после настройки ведения журнала. Если необходимо более длительное хранение, настройте приложение для записи выходных данных в контейнер больших двоичных объектов. Журналы приложений Java и Tomcat можно найти в `/home/LogFiles/Application/` каталога.

Если приложение использует [Logback](https://logback.qos.ch/) или [Log4j](https://logging.apache.org/log4j) для трассировки, то эти данные трассировки можно передать в Azure Application Insights для просмотра, выполнив инструкции по настройке платформы ведения журнала в разделе [Просмотр журналов трассировки Java в Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Средства устранения неполадок

На основе встроенных образов Java [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) операционной системы. Используйте `apk` диспетчер пакетов для установки Устранение неполадок средства или команды.

## <a name="customization-and-tuning"></a>Настройка

Служба приложений Azure для Linux поддерживает настройку с помощью портала Azure и интерфейса командной строки. Ознакомьтесь со следующими статьями о настройке конкретных веб-приложений не на платформе Java:

- [Настройка параметров службы приложений](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Настройка личного домена](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Включить SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Добавление CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Настройка на сайт Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Настройка параметров среды выполнения Java

Чтобы задать объем выделенной памяти и других параметров среды выполнения виртуальной машины Java в средах Java SE и Tomcat, создайте [параметр приложения](/azure/app-service/web-sites-configure#app-settings) с именем `JAVA_OPTS` с параметрами. При запуске служба приложений для Linux передает этот параметр в качестве переменной среды в среду выполнения Java.

На портале Azure в разделе **Параметры приложения** для веб-приложения создайте параметр приложения `JAVA_OPTS`, включающий в себя дополнительные параметры, такие как `-Xms512m -Xmx1204m`.

Чтобы настроить параметр приложения из подключаемого модуля Maven, добавьте значение параметра/тегов в разделе подключаемый модуль Azure. В следующем примере задаются минимальный и максимальный размеры кучи для Java.

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

Если вы развертываете приложение JAR-ФАЙЛ, его имя должно `app.jar` таким образом, чтобы встроенного изображения мог корректно идентифицировать приложение. (Подключаемый модуль Maven делает этот переименование автоматически.) Если вы не хотите переименовать JAR-ФАЙЛУ для `app.jar`, вы можете отправить сценарий оболочки с помощью команды для запуска JAR-ФАЙЛУ. Вставьте полный путь для этого сценария в [файл запуска](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-faq#startup-file) текстовое поле в разделе конфигурации портала.

### <a name="turn-on-web-sockets"></a>Включение веб-сокетов

Включите для приложения поддержку веб-сокетов на портале Azure в разделе **Параметры приложения**. Необходимо будет перезапустить приложение, чтобы этот параметр вступил в силу.

Включите поддержку веб-сокетов с помощью Azure CLI, выполнив следующую команду.

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true
```

Затем перезапустите приложение.

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
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

Настройте аутентификацию приложения на портале Azure с помощью параметра **Проверка подлинности и авторизация**. Вы можете включить аутентификацию с помощью Azure Active Directory или имен для входа в социальные сети, таких как Facebook, Google или GitHub. На портале Azure можно настроить только один поставщик аутентификации.  Дополнительные сведения приведены в разделе [Настройка приложения службы приложений для использования входа с помощью Azure Active Directory](/azure/app-service/configure-authentication-provider-aad) и связанных статьях о других поставщиках удостоверений.

Если необходимо включить несколько поставщиков входа, следуйте инструкциям в статье [Настройка проверки подлинности и авторизации в службе приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to).

Разработчики для Spring Boot могут использовать [краткое руководство по использованию Spring Boot и Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable), чтобы защитить приложения с помощью привычных заметок и интерфейсов API Spring Security. Увеличьте максимальный размер заголовка в файле `application.properties`. Мы рекомендуем использовать значение `16384`.

### <a name="configure-tlsssl"></a>Настройка TLS/SSL

Следуйте инструкциям в разделе [Руководство. Привязывание существующего настраиваемого SSL-сертификата к веб-приложениям Azure](/azure/app-service/app-service-web-tutorial-custom-ssl), чтобы передать существующий SSL-сертификат и привязать его к доменному имени приложения. По умолчанию приложение по-прежнему будет разрешать HTTP-подключения. Выполните соответствующие инструкции в этом руководстве, чтобы принудительно включить SSL и TLS.

### <a name="use-keyvault-references"></a>Использование ссылок на хранилище ключей

[Хранилище ключей Azure](../../key-vault/key-vault-overview.md) обеспечивает централизованное управление секретами с журналом политики и аудита доступа. Можно хранить секретные данные (например, пароли или строки подключения) в хранилище ключей и использовать эти секреты в приложении посредством переменных среды.

Во-первых, следуйте инструкциям по [предоставления вашему приложению доступ к Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) и [делает ссылку на секрет хранилища ключей в параметр приложения](../app-service-key-vault-references.md#reference-syntax). Вы можете проверить, что ссылка разрешается секрет с печатью переменную среды при удаленном входе терминалов службы приложений.

Чтобы внедрить эти секреты в файле конфигурации Spring или Tomcat, используйте синтаксис переменной путем внедрения кода среды (`${MY_ENV_VAR}`). Файлы конфигурации Spring, см в этой документации по [выразили конфигураций](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="data-sources"></a>Источники данных

### <a name="tomcat"></a>Tomcat

Эти инструкции применимы ко всем подключениям к базе данных. Необходимо будет заменить значения заполнителей на имя класса драйвера и JAR-файл выбранной базы данных. Ниже приведена таблица с именами классов и ссылками для скачивания драйверов для распространенных баз данных.

| База данных   | Имя класса драйвера                             | Драйвер JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Download (Скачать)](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Скачать](https://dev.mysql.com/downloads/connector/j/) (выберите "Platform Independent" (Независимо от платформы)) |
| SQL Server; | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Download (Скачать)](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Чтобы настроить Tomcat для использования Java Database Connectivity (JDBC) или Java Persistence API (JPA), сначала настройте переменную среды `CATALINA_OPTS`, считываемую Tomcat при запуске. Задайте эти значения с помощью параметра приложения в [подключаемом модуле Maven для службы приложений](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>  
    <property>  
        <name>CATALINA_OPTS</name>  
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>  
    </property>  
</appSettings>  
```

Переменные среды можно также задать в колонке "Параметры приложения" на портале Azure.

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

    ```bash
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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Наконец установите драйвер JAR-файлы в пути к классам Tomcat и перезапустите службу приложений

1. Убедитесь, что файлы драйвера JDBC доступны для загрузчика классов Tomcat, разместив их в каталоге `/home/tomcat/lib`. (Если этот каталог отсутствует, создайте его.) Чтобы передать эти файлы в экземпляр службы приложений, выполните следующие действия.  
   1. Установите расширение webpp для Службы приложения Azure.

      ```azurecli-interactive
      az extension add –name webapp
      ```

   1. Выполните следующую команду интерфейса командной строки, чтобы создать туннель SSH между локальной системой и службой приложений.

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

   1. Подключитесь к локальному порту туннелирования с помощью клиента SFTP и передайте эти файлы в папку `/home/tomcat/lib`.

      Кроме того, драйвер JDBC можно отправить с помощью FTP-клиента. Чтобы получить учетные данные FTP, следуйте этим [инструкциям](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Если вы создали источник данных на уровне сервера, перезапустите приложение Linux службы приложений. Tomcat сбросит `CATALINA_HOME` до значения `/home/tomcat/conf` и будет использовать обновленную конфигурацию.

### <a name="spring-boot"></a>Spring Boot

Чтобы подключиться к источникам данных в приложениях Spring Boot, мы рекомендуем Создание строк подключения и вставляя их в вашей `application.properties` файл.

1. В разделе «Параметры приложения» в колонке службы приложений задайте имя для строки, вставьте строку подключения JDBC в соответствующее поле и задайте для этого типа «Custom». Можно дополнительно задать эту строку подключения как параметр слота.

    ![Создание строки подключения на портале.][1]

    Эта строка подключения имеет доступ к нашему приложению как переменную среды с именем `CUSTOMCONNSTR_<your-string-name>`. Например, строка подключения, созданных ранее будет называться `CUSTOMCONNSTR_exampledb`.

2. В вашей `application.properties` файл, ссылаются на эту строку подключения — с именем переменной среды. В нашем примере мы используем следующее выражение.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

См. в разделе [документации Spring Boot на доступ к данным](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html
) и [выразили конфигураций](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) Дополнительные сведения по этой теме.

## <a name="docker-containers"></a>контейнеры Docker;

Чтобы использовать в своих контейнерах поддерживаемый в Azure пакет JDK Zulu, обязательно используйте готовые образы, предоставленные на [странице скачивания Azul Zulu Enterprise для Azure](https://www.azul.com/downloads/azure-only/zulu/), или используйте примеры `Dockerfile` из [репозитория Майкрософт для Java на сайте GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Доступность среды выполнения и заявление о поддержке

Служба приложений для Linux поддерживает две среды выполнения для управляемого размещения веб-приложений Java.

- [Контейнер сервлетов Tomcat](https://tomcat.apache.org/) для запуска приложений, которые упакованы как файлы веб-архива (WAR-файлы). Поддерживаются версии 8.5 и 9.0.
- Среда выполнения Java SE для запуска приложений, которые упакованы как файлы архива Java (JAR-файлы). Сейчас поддерживается только основной номер версии 8.

## <a name="java-runtime-statement-of-support"></a>Заявление о поддержке среды выполнения Java

### <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Azure поддерживает пакет Java Development Kit (JDK) [Zulu](https://www.azul.com/downloads/azure-only/zulu/), предоставляемый компанией [Azul Systems](https://www.azul.com/).

Обновления для основного номера версии будут предоставляться посредством новых вариантов среды выполнения в Службе приложений Azure для Linux. Пользователи, устанавливающие более новые версии Java посредством настройки развернутой службы приложений, несут ответственность за тестирование выбранных обновлений для основного номера версии и их соответствие своим потребностям.

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре.

### <a name="security-updates"></a>обновления для системы безопасности;

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

### <a name="local-development"></a>Локальная разработка

Разработчики могут скачать выпуск Production Edition пакета Azul Zulu Enterprise JDK для локальной разработки с [сайта загрузки Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Поддержка разработки

Техническая поддержка [пакета Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) при разработке для Azure или [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) предоставляется при наличии [соответствующего плана поддержки Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Поддержка времени выполнения

Разработчики могут [сообщить о проблеме](/azure/azure-supportability/how-to-create-azure-support-request) с Azul Zulu JDK в службу поддержки Azure при наличии [соответствующего плана поддержки](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Дальнейшие действия

Посетите центр [Azure для разработчиков Java](/java/azure/), чтобы найти краткие руководства Azure, руководства и справочную документацию по Java.

<!--Image references-->
[1]: ./media/app-service-linux-java/connection-string.png
