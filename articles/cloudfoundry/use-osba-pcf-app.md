---
title: Использование базы данных, созданной с помощью Open Service Broker для Azure, с приложением в Pivotal Cloud Foundry
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: В этой статье описывается, как настроить приложение Pivotal Cloud Foundry для использования базы данных, созданной с помощью Open Service Broker для Azure
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258881"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>Руководство. Использование базы данных, созданной с помощью Open Service Broker для Azure, с приложением в Pivotal Cloud Foundry

Использование Open Service Broker для Azure с экземпляром Pivotal Cloud Foundry позволяет предоставлять в Azure службы, такие как базы данных, непосредственно из командной строки Cloud Foundry и экземпляра Pivotal Cloud Foundry. Кроме того, эти службы можно привязать к приложению, работающему в экземпляре Pivotal Cloud Foundry. При такой привязке приложения к службе нет необходимости обновлять какой-либо код или конфигурацию в приложении. В этой статье объясняется, как использовать службу Open Service Broker для Azure для базы данных с приложением в Pivotal Cloud Foundry.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Подготовка пространства приложения в Pivotal Cloud Foundry.
> * Клонирование источника примера приложения с GitHub.
> * Подготовка приложения для развертывания.
> * Разверните приложения.
> * Создание базы данных с помощью Open Service Broker для Azure.
> * Привязка базы данных к приложению.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем продолжить, необходимо выполнить следующее:

* [Установите и настройте Pivotal Cloud Foundry](create-cloud-foundry-on-azure.md).
* [Установите и настройте Open Service Broker для Azure](https://network.pivotal.io/products/azure-open-service-broker-pcf) в вашем экземпляре Cloud Foundry.

Ниже приведен пример окна Pivotal Cloud Foundry в Operations Manager с установленной и настроенной плиткой Open Service Broker для Azure:

![Pivotal Cloud Foundry с Open Service Broker для Azure установлен](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Подготовка пространства приложения в Pivotal Cloud Foundry

Чтобы развернуть приложение в экземпляре Pivotal Cloud Foundry, необходимо войти в систему с помощью средства командной строки `cf`. Нужно также указать желаемую организацию и пространство.

Чтобы убедиться, что вы вошли в систему, и отобразить целевое пространство, используйте `cf target`. В приведенном ниже примере показано, что пользователь уже вошел как *администратор*, используя организацию *myorg* и целевое пространство *dev*:

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

Чтобы выполнить вход, воспользуйтесь `cf login`:

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Чтобы создать новую организацию и пространство, используйте `cf create-org` и `cf create-space`:

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

Чтобы указать пространство, используйте `cf target`.

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>Получение кода приложения

Чтобы приложение могло взаимодействовать с Open Service Broker для Azure, в качестве источника данных оно должно использовать, например, базу данных. В этой статье мы воспользуемся [примером приложения Spring Music из Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music), чтобы продемонстрировать приложение, использующее источник данных.

Клонируйте приложение с GitHub и перейдите в его каталог:

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> Чтобы просмотреть источники данных для этого приложения, откройте файл [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml).


## <a name="prepare-the-application-for-deployment"></a>Подготовка приложения к развертыванию

Прежде чем развернуть приложение в экземпляре Pivotal Cloud Foundry, его необходимо создать. В целях демонстрации включим ведение журнала *отладки*, который будет регистрировать сведения о подключении к источнику данных.

Чтобы включить ведение журнала *отладки* для сбора сведений о подключении к базе данных, добавьте следующее свойство yaml в конец файла *application.yml*:

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

Пример приложения использует Gradle, чтобы интегрировать приложение в запускаемый JAR-файл Spring Boot. Запускаемый JAR-файл будет развернут в экземпляре Pivotal Cloud Foundry. Чтобы создать приложение:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>Развертывание приложения

Чтобы развернуть приложение в экземпляре Pivotal Cloud Foundry, используйте команду `cf push`.

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

Скопируйте значение из поля *routes*, содержащегося в выходных данных команды `cf push`. Маршрут — это URL-адрес, который будет использоваться для доступа к запущенному приложению. Например: 

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


После развертывания приложения можно просмотреть его журналы, чтобы увидеть URL-адрес подключения, используемый приложением. Приведенная ниже команда выводит журналы приложения и ищет конфигурацию *jdbcUrl* с помощью `grep`.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

Обратите внимание, что приложение использует *h2:mem:testdb*, которая является выполняющейся в памяти базой данных. Приложение Spring автоматически настраивается на использование выполняющейся в памяти базы данных, если зависимость от такой базы данных определена в пути к классу и включена [автоматическая настройка](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html). В примере приложения [настроена зависимость от выполняющейся в памяти базы данных h2](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49) и включена автоматическая конфигурация в [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8).

Используйте маршрут приложения, чтобы перейти к нему в браузере. Маршрут (URL-адрес) отображается в выходных данных команды `cf push`.

> [!TIP]
> Вы также можете отобразить URL-адрес приложения, выполнив `cf apps`.

После того как вы перейдете к приложению в браузере, удалите в нем несколько существующих альбомов и создайте несколько новых. Чтобы сохранить изменения, пример приложения использует выполняющуюся в памяти базу данных. Вы также заметите, что приложение заполнено некоторыми [данными по умолчанию](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). 

![Приложение Spring Music с данными по умолчанию](media/music-app.png)

Так как приложение использует выполняющуюся в памяти базу данных, изменения не будут сохранены при перезапуске или повторном развертывании приложения. Чтобы увидеть, что изменения не сохраняются, внесите изменения и повторно разместите приложение с помощью команды `cf restage`:

```cmd
cf restage spring-music
```

После повторного размещения приложения откройте его в браузере, используя тот же URL-адрес. Обратите внимание, что внесенные изменения исчезли и отображаются данные по умолчанию.

![Приложение Spring Music с данными по умолчанию](media/music-app.png)

## <a name="create-a-database"></a>Создание базы данных

Чтобы создать постоянную базу данных в Azure с помощью Open Service Broker, выполните команду `cf create-service`. Приведенная ниже команда подготавливает базу данных PostgreSQL в Azure в группе ресурсов *MyResourceGroup* в регионе *eastus*. Дополнительные сведения о *resourceGroup*, *location* и других связанных с Azure параметрах JSON см. в справочной документации по модулю [PostgreSQL](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision).

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

База данных предоставляется в экземпляре Pivotal Cloud Foundry как служба с именем *mypgsql*. Когда база данных будет подготовлена (это займет несколько минут), ее можно привязать к приложению. Чтобы убедиться, что подготовка базы данных завершена, выполните команду `cf services`.

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

После завершения подготовки значением параметра *last operation* для службы будет *create succeeded*.

## <a name="bind-the-database-to-your-application"></a>Привязка базы данных к приложению

Привяжите службу к приложению с помощью команды `bind-service`.

```cmd
cf bind-service spring-music mypgsql
```

После привязки службы к приложению необходимо повторно разместить приложение, чтобы изменения вступили в силу.

```cmd
cf restage spring-music
```

Приложение [настроено для использования соединителей Spring Cloud](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46), благодаря чему ваш экземпляр Pivotal Cloud Foundry может применять [автоматическую перенастройку](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto) источника данных приложения. В этом случае экземпляр Pivotal Cloud Foundry автоматически перенастраивает приложение для использования службы, привязанной к источнику данных, при повторном размещении приложения.

После повторного размещения приложения оно будет использовать *mypgsql* для хранения данных вместо выполняющейся в памяти базы данных.

Теперь все внесенные изменения при перезапуске и повторном развертывании будут сохранены. Вы также можете увидеть используемый приложением URL-адрес подключения, просмотрев журналы приложения еще раз.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

Обратите внимание на две записи:

* Исходное значение *h2:mem:testdb*.
* Обновленное значение базы данных PostgreSQL после повторного размещения приложения.

Чтобы проверить, сохраняются ли данные в базе данных PostgreSQL, откройте приложение в браузере, внесите изменения, а затем повторно разместите его.

```cmd
cf restage spring-music
```

После повторного размещения приложения откройте его в браузере, используя тот же URL-адрес. Обратите внимание, что внесенные изменения не исчезнут.

## <a name="cleanup"></a>Очистка

Если вы хотите отключить приложение от базы данных, вы можете отменить привязку с помощью команды `cf unbind-service`.

```cmd
cf unbind-service spring-music mypgsql
```

Аналогично привязке приложения к службе необходимо повторно разместить приложение, чтобы изменения вступили в силу. Это позволит сохранить содержимое *mypgsql* и приложения, но при этом вместо *mypgsql* приложение начнет использовать выполняющуюся в памяти базу данных.

Чтобы удалить базу данных, выполните команду `cf delete-service`. *Вы не сможете отменить это действие, поэтому убедитесь, что точно хотите удалить базу данных, прежде чем продолжить.*

```cmd
cf delete-service mypgsql
```

Чтобы удалить приложение из экземпляра Pivotal Cloud Foundry:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве мы рассмотрели развертывание приложения в Pivotal Cloud Foundry, а также создание базы данных с помощью Open Service Broker для Azure. В нем также рассматривалась привязка базы данных к приложению в вашем экземпляре Pivotal Cloud Foundry. Дополнительные сведения о развертывании приложений в Cloud Foundry в Azure см. в следующих статьях:

* [Cloud Foundry в Azure](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Развертывание первого приложения в Cloud Foundry в Microsoft Azure](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)