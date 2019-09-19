---
title: Настройка приложений Windows на Java — служба приложений Azure | Документация Майкрософт
description: Узнайте, как настроить приложения Java для запуска на экземплярах Windows по умолчанию в службе приложений Azure.
keywords: служба приложений Azure, веб-приложение, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 91f0c059d22fb921aeb0c65f7d4eba95debd530d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097735"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Настройка приложения Windows Java для службы приложений Azure

Служба приложений Azure позволяет разработчикам Java быстро создавать, развертывать и масштабировать свои веб-приложения Tomcat в полностью управляемой службе на основе Windows. Возможно развертывание приложений с подключаемыми модулями Maven из командной строки или в редакторах, например Visual Studio Code, Eclipse или IntelliJ.

Это краткое описание содержит основные понятия и инструкции для разработчиков Java, использующих службу приложений. Если вы никогда не использовали службу приложений Azure, сначала ознакомьтесь с [кратким](app-service-web-get-started-java.md) руководством по Java. Ответы на часто задаваемые вопросы об использовании службы приложений, не относящейся к разработке Java, приведены в статье [вопросы и ответы по Windows в службе приложений](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Развертывание приложения

Для развертывания WAR-файлов можно использовать [подключаемый модуль Maven для службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . Развертывание с популярными IDE также поддерживается с [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) или [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

В противном случае ваш метод развертывания будет зависеть от типа архива:

- Чтобы развернуть файлы WAR в Tomcat, используйте конечную точку `/api/wardeploy/` для публикации файла архива. Дополнительные сведения об этом API см. в [этой документации](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).

Не развертывайте WAR-файл с помощью FTP. Средство FTP предназначено для передачи сценариев запуска, зависимостей или других файлов среды выполнения. Оно не является оптимальным решением для развертывания веб-приложений.

## <a name="logging-and-debugging-apps"></a>Ведение журнала и отладка приложений

Отчеты о производительности, визуализация трафика и проверка работоспособности доступны на портале Azure для каждого приложения. Дополнительные сведения см. в статье [Обзор диагностики службы приложений Azure](overview-diagnostics.md).

### <a name="ssh-console-access"></a>Доступ к консоли SSH

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Дополнительные сведения см. [в статье потоковая передача журналов в Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Ведение журнала приложений

Включите [ведение журнала приложений](troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enable-application-logging-windows) с помощью портала Azure или [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config), чтобы настроить службу приложений для записи выходных данных стандартной консоли приложения и потоков ошибок стандартной консоли в локальную файловую систему или хранилище BLOB-объектов Azure. Запись журналов в локальную файловую систему экземпляра службы приложений отключается через 12 часов после настройки ведения журнала. Если необходимо более длительное хранение, настройте приложение для записи выходных данных в контейнер больших двоичных объектов. Журналы приложений Java и Tomcat можно найти в каталоге */логфилес/аппликатион/* .

Если приложение использует [Logback](https://logback.qos.ch/) или [Log4j](https://logging.apache.org/log4j) для трассировки, то эти данные трассировки можно передать в Azure Application Insights для просмотра, выполнив инструкции по настройке платформы ведения журнала в разделе [Просмотр журналов трассировки Java в Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Настройка

Служба приложений Azure поддерживает настройку и настройку с помощью портал Azure и интерфейса командной строки. Ознакомьтесь со следующими статьями для конфигурации веб-приложения, не относящегося к Java:

- [Настройка параметров приложения](configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Настройка личного домена](app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Включение SSL](app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Добавление CDN](../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Настройка сайта KUDU](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Настройка параметров среды выполнения Java

Чтобы задать выделенную память или другие параметры среды выполнения виртуальной машины Java, создайте [параметр приложения](configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) с именем `JAVA_OPTS` с параметрами. Служба приложений передает этот параметр в среду выполнения Java при запуске в качестве переменной среды.

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

### <a name="pre-compile-jsp-files"></a>Предварительная компиляция файлов JSP

Чтобы повысить производительность приложений Tomcat, можно скомпилировать файлы JSP перед развертыванием в службе приложений. Вы можете использовать [подключаемый модуль Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) , предоставляемый Apache слинг, или использовать этот [Ant файл сборки](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Защита приложений

Приложения Java, работающие в службе приложений, имеют тот же набор [рекомендаций по обеспечению безопасности](/azure/security/security-paas-applications-using-app-services) , что и другие приложения.

### <a name="authenticate-users-easy-auth"></a>Проверка подлинности пользователей (простая проверка подлинности)

Настройте проверку подлинности приложения в портал Azure с помощью параметра **Проверка подлинности и авторизация** . Вы можете включить аутентификацию с помощью Azure Active Directory или имен для входа в социальные сети, таких как Facebook, Google или GitHub. На портале Azure можно настроить только один поставщик аутентификации. Дополнительные сведения приведены в разделе [Настройка приложения службы приложений для использования входа с помощью Azure Active Directory](configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) и связанных статьях о других поставщиках удостоверений. Если необходимо включить несколько поставщиков входа, следуйте инструкциям в статье [Настройка проверки подлинности и авторизации в службе приложений Azure](app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

#### <a name="tomcat-and-wildfly"></a>Tomcat и Вилдфли

Приложение Tomcat или Вилдфли может получить доступ к утверждениям пользователя непосредственно из сервлета путем приведения объекта Principal к объекту Map. Объект Map будет сопоставлять каждый тип утверждения с коллекцией утверждений для этого типа. В приведенном ниже `request` коде является `HttpServletRequest`экземпляром.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Теперь можно проверить `Map` объект на наличие конкретного утверждения. Например, следующий фрагмент кода выполняет перебор всех типов заявок и выводит содержимое каждой коллекции.

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

Чтобы отключить эту функцию, создайте параметр приложения с именем `WEBSITE_AUTH_SKIP_PRINCIPAL` и `1`значением. Чтобы отключить все фильтры сервлета, добавленные службой приложений, создайте параметр с `WEBSITE_SKIP_FILTERS` именем со `1`значением.

### <a name="configure-tlsssl"></a>Настройка TLS/SSL

Следуйте инструкциям в разделе [Руководство. Привязывание существующего настраиваемого SSL-сертификата к веб-приложениям Azure](app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), чтобы передать существующий SSL-сертификат и привязать его к доменному имени приложения. По умолчанию приложение по-прежнему будет разрешать HTTP-подключения. Выполните соответствующие инструкции в этом руководстве, чтобы принудительно включить SSL и TLS.

### <a name="use-keyvault-references"></a>Использование ссылок KeyVault

[Azure KeyVault](../key-vault/key-vault-overview.md) обеспечивает централизованное управление секретами с помощью политик доступа и журнала аудита. Вы можете хранить секреты (например, пароли или строки подключения) в KeyVault и обращаться к этим секретам в приложении с помощью переменных среды.

Сначала следуйте инструкциям, [чтобы предоставить приложению доступ к Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) и [сделать ссылку KeyVault на секрет в параметре приложения](app-service-key-vault-references.md#reference-syntax). Можно проверить, что ссылка разрешается в секрет, выполнив печать переменной среды во время удаленного доступа к терминалу службы приложений.

Чтобы внедрить эти секреты в файл конфигурации весны или Tomcat, используйте синтаксис внедрения переменных среды (`${MY_ENV_VAR}`). Дополнительные сведения о файлах конфигурации пружины см. в этой документации по [внешним конфигурациям](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Настройка платформ APM

В этом разделе показано, как подключить приложения Java, развернутые в службе приложений Azure на платформе Linux, с помощью платформ мониторинга производительности приложений NewRelic и AppDynamics (APM).

### <a name="configure-new-relic"></a>Настройка New Relic

1. Создание новой учетной записи Relic по адресу [NewRelic.com](https://newrelic.com/signup)
2. Скачайте агент Java из NewRelic, он будет иметь имя файла, аналогичное *неврелик-Жава-КС. x. x. zip*.
3. Скопируйте ключ лицензии. Он понадобиться позже для настройки агента.
4. С помощью [консоли KUDU](https://github.com/projectkudu/kudu/wiki/Kudu-console) создайте новый каталог */Хоме/Сите/ввврут/АПМ*.
5. Передайте распакованные новые файлы агента Java Relic в каталог в папке */Хоме/Сите/ввврут/АПМ*. Файлы для агента должны быть в */Хоме/Сите/ввврут/АПМ/неврелик*.
6. Измените файл YAML по адресу */Хоме/Сите/ввврут/АПМ/неврелик/неврелик.ИМЛ* и замените значение лицензии заполнителя на свой собственный лицензионный ключ.
7. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если приложение использует **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Настройка AppDynamics

1. Создайте учетную запись AppDynamics на сайте [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Скачайте агент Java с веб-сайта AppDynamics, имя файла будет похоже на *аппсерверажент-КС. x. x. xxxxx. zip* .
3. С помощью [консоли KUDU](https://github.com/projectkudu/kudu/wiki/Kudu-console) создайте новый каталог */Хоме/Сите/ввврут/АПМ*.
4. Отправьте файлы агента Java в каталог в разделе */Хоме/Сите/ввврут/АПМ*. Файлы для агента должны быть в */Хоме/Сите/ввврут/АПМ/аппдинамикс*.
5. На портале Azure перейдите к приложению в службе приложений и создайте параметр приложения.
    - Если вы используете **Java SE**, создайте переменную среды `JAVA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.
    - Если вы используете **Tomcat**, создайте переменную среды `CATALINA_OPTS` со значением `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, где `<app-name>` — имя службы приложений.

>  Если у вас уже есть переменная среды `JAVA_OPTS` или `CATALINA_OPTS`, добавьте параметр `-javaagent:/...` в конец текущего значения.

## <a name="data-sources"></a>Источники данных

### <a name="tomcat"></a>Tomcat

Эти инструкции применимы ко всем подключениям к базе данных. Необходимо будет заменить значения заполнителей на имя класса драйвера и JAR-файл выбранной базы данных. Ниже приведена таблица с именами классов и ссылками для скачивания драйверов для распространенных баз данных.

| База данных   | Имя класса драйвера                             | Драйвер JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Загрузить](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Скачать](https://dev.mysql.com/downloads/connector/j/) (выберите "Platform Independent" (Независимо от платформы)) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Загрузить](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Чтобы настроить Tomcat для использования Java Database Connectivity (JDBC) или API сохраняемости Java (JPA), сначала настройте `CATALINA_OPTS` переменную среды, которая считывается в Tomcat при запуске. Задайте эти значения с помощью параметра приложения в [подключаемом модуле Maven для службы приложений](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Или задайте переменные среды на странице**Параметры приложения** **конфигурации** > в портал Azure.

Затем определите, должен ли источник данных быть доступным для одного приложения или для всех приложений, работающих в сервлете Tomcat.

#### <a name="application-level-data-sources"></a>Источники данных на уровне приложения

1. Создайте файл *context. XML* в каталоге *META-INF или* проекте. Создайте *файл META-INF или* каталог, если он не существует.

2. В *context. XML*добавьте `Context` элемент, чтобы связать источник данных с адресом JNDI. Замените заполнитель `driverClassName` именем класса драйвера из приведенной выше таблицы.

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

#### <a name="finalize-configuration"></a>Завершение конфигурации

Наконец, мы поместим драйвер JAR в путь к классам Tomcat и перезапустите службу приложений. Убедитесь, что файлы драйвера JDBC доступны для Tomcat класслоадер, поместив их в каталог */Хоме/томкат/либ* . (Если этот каталог отсутствует, создайте его.) Чтобы передать эти файлы в экземпляр службы приложений, выполните следующие действия.

1. В [Cloud Shell](https://shell.azure.com)установите расширение webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Выполните следующую команду CLI, чтобы создать туннель SSH из локальной системы в службу приложений:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Подключитесь к локальному порту туннелирования с помощью клиента SFTP и отправьте файлы в папку */Хоме/томкат/либ* .

Кроме того, драйвер JDBC можно отправить с помощью FTP-клиента. Чтобы получить учетные данные FTP, следуйте этим [инструкциям](deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="configuring-tomcat"></a>Настройка Tomcat

Чтобы изменить Tomcat `server.xml` или другие файлы конфигурации, сначала запишите основную версию Tomcat на портале.

1. Найдите корневой каталог Tomcat для своей версии, выполнив `env` команду. Найдите переменную среды, которая начинается с `AZURE_TOMCAT`и соответствует основной версии. Например, `AZURE_TOMCAT85_HOME` указывает на каталог Tomcat для Tomcat 8,5.
1. Определив корневой каталог Tomcat для своей версии, скопируйте каталог конфигурации в `D:\home`папку. Например, если `AZURE_TOMCAT85_HOME` имело `D:\Program Files (x86)\apache-tomcat-8.5.37`значение, новый путь к копируемому каталогу будет иметь `D:\home\apache-tomcat-8.5.37`вид.

Наконец, перезапустите службу приложений. Развертывания должны быть так же `D:\home\site\wwwroot\webapps` , как и раньше.

## <a name="java-runtime-statement-of-support"></a>Заявление о поддержке среды выполнения Java

### <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Azure поддерживает пакет Java Development Kit (JDK) [Zulu](https://www.azul.com/downloads/azure-only/zulu/), предоставляемый компанией [Azul Systems](https://www.azul.com/).

Обновления основной версии будут предоставляться с помощью новых параметров среды выполнения в службе приложений Azure для Windows. Пользователи, устанавливающие более новые версии Java посредством настройки развернутой службы приложений, несут ответственность за тестирование выбранных обновлений для основного номера версии и их соответствие своим потребностям.

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре. Дополнительные сведения об Java в Azure см. в [этом документе поддержки](https://docs.microsoft.com/azure/java/jdk/).

### <a name="security-updates"></a>Обновления для системы безопасности

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

### <a name="local-development"></a>Локальная разработка

Разработчики могут скачать выпуск Production Edition пакета Azul Zulu Enterprise JDK для локальной разработки с [сайта загрузки Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Поддержка разработки

Поддержка продуктов для [Zulu JDK, поддерживаемых Azure](https://www.azul.com/downloads/azure-only/zulu/) , доступна в корпорации Майкрософт при разработке для Azure или [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) с [полным планом поддержки Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Поддержка времени выполнения

Разработчики могут [сообщить о проблеме](/azure/azure-supportability/how-to-create-azure-support-request) с Azul Zulu JDK в службу поддержки Azure при наличии [соответствующего плана поддержки](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Следующие шаги

В этом разделе содержится инструкция поддержки Java для службы приложений Azure в Windows.

- Дополнительные сведения о размещении веб-приложений с помощью службы приложений Azure см. в статье [Обзор службы приложений](overview.md).
- Дополнительные сведения о Java в разработке Azure см. в статье [центр разработки для Java в Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
