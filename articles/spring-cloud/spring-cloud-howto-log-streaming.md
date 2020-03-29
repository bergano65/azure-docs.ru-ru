---
title: Потоковая передача журналов приложения в Azure Spring Cloud в реальном времени
description: Как использовать потоковую передачу журналов для мгновенного просмотра журналов приложений
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192206"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Потоковая передача журналов приложения в Azure Spring Cloud в реальном времени
Облако Azure Spring Cloud позволяет потоку журналов в Azure CLI получать журналы консолей приложений в режиме реального времени для устранения неполадок. Вы также можете [анализировать журналы и метрики с настройками диагностики.](./diagnostic-services.md)

## <a name="prerequisites"></a>Предварительные требования

* Установите [расширение Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) для весеннего облака, минимальная версия 0.2.0 .
* Пример **облачности Azure Spring** с запущенным приложением, например [приложение Spring Cloud.](./spring-cloud-quickstart-launch-app-cli.md)

> [!NOTE]
>  Расширение ASC CLI обновляется с версии 0.2.0 до 0.2.1. Это изменение влияет на синтаксис команды `az spring-cloud app log tail`для потоковой передачи журнала: , которая заменяется: `az spring-cloud app logs`. Команда: `az spring-cloud app log tail` будет уволен в будущем релизе. Если вы использовали версию 0.2.0, вы можете обновить до 0.2.1. Во-первых, удалите старую версию с командой: `az extension remove -n spring-cloud`.  Затем установите 0.2.1 командой: `az extension add -n spring-cloud`.

## <a name="use-cli-to-tail-logs"></a>Используйте CLI для хвостовых журналов

Чтобы избежать повторного указания группы ресурсов и имени экземпляра службы, установите имя группы ресурсов по умолчанию и имя кластера.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
В следующих примерах группа ресурсов и имя службы будут опущены в командах.

### <a name="tail-log-for-app-with-single-instance"></a>Хвост журнала для приложения с одним экземпляром
Если приложение под названием auth-service имеет только один экземпляр, вы можете просмотреть журнал экземпляра приложения со следующей командой:
```
az spring-cloud app logs -n auth-service
```
Это позволит вернуть журналы:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Хвост журнала для приложения с несколькими экземплярами
Если для приложения с именем `auth-service`существует несколько экземпляров, `-i/--instance` можно просмотреть журнал экземпляров, используя опцию. 

Во-первых, вы можете получить имена экземпляров приложения со следующей командой.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
С результатами:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Затем можно передавать журналы экземпляра приложения `-i/--instance` с опцией:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Вы также можете получить подробную информацию о экземплярах приложений с портала Azure.  После выбора **приложений** в левом навигационном стеле облачного сервиса Azure Spring выберите **экземпляры приложений.**

### <a name="continuously-stream-new-logs"></a>Непрерывно потоковое новые журналы
По умолчанию `az spring-cloud ap log tail` печатает только существующие журналы, перетекаемые на консоль приложения, а затем выходы. Если вы хотите передавать новые журналы, добавьте -f (--следуйте):  

```
az spring-cloud app logs -n auth-service -f
``` 
Чтобы проверить все варианты регистрации поддерживается:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Дальнейшие действия

* [Анализ журналов и метрик с настройками диагностики](./diagnostic-services.md)

 





