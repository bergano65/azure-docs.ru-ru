---
title: Потоковая передача журналов облачного приложения Azure весны в режиме реального времени
description: Как мгновенно просматривать журналы приложений с помощью потоковой передачи журналов
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fa2e7af51ff681da0bfdac928cc08bf75126a3b8
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156426"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Потоковая передача журналов облачного приложения Azure весны в режиме реального времени
Azure Веснного облака позволяет выполнять потоковую передачу журналов в Azure CLI для получения журналов консоли приложений в режиме реального времени для устранения неполадок. Вы также можете [анализировать журналы и метрики с помощью параметров диагностики](./diagnostic-services.md).

## <a name="prerequisites"></a>Технические условия

* Установите [расширение Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) для пружинного облака, минимальной версии 0.2.0.
* Экземпляр **Azure веснного облака** с выполняющимся приложением, например [пружинное приложение Cloud](./spring-cloud-quickstart-launch-app-cli.md).

## <a name="use-cli-to-tail-logs"></a>Использование интерфейса командной строки для заключительного фрагмента журналов

Чтобы избежать повторного указания имени группы ресурсов и экземпляра службы, задайте имя группы ресурсов по умолчанию и имя кластера.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
В следующих примерах группа ресурсов и имя службы будут пропущены в командах.

### <a name="tail-log-for-app-with-single-instance"></a>Заключительный фрагмент журнала для приложения с одним экземпляром
Если приложение с именем auth-Service имеет только один экземпляр, журнал экземпляра приложения можно просмотреть с помощью следующей команды:
```
az spring-cloud app log tail -n auth-service
```
Будут возвращены журналы:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Заключительный фрагмент журнала для приложения с несколькими экземплярами
Если для приложения существует несколько экземпляров с именем `auth-service`, журнал экземпляра можно просмотреть с помощью параметра `-i/--instance`. Например, можно выполнить потоковую передачу журнала экземпляра одного приложения, указав имя приложения и имя экземпляра:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```
Кроме того, можно получить экземпляры приложения из портал Azure. 
1. Перейдите к группе ресурсов и выберите свой экземпляр облака Azure весны.
1. В разделе Общие сведения об экземпляре Azure весны облака выберите **приложения** в левой области навигации.
1. Выберите свое приложение, а затем щелкните **экземпляры приложения** в левой области навигации. 
1. Будут отображены экземпляры приложений.

### <a name="continuously-stream-new-logs"></a>Непрерывный поток новых журналов
По умолчанию `az spring-cloud ap log tail` выводит только существующие журналы, переданные в консоль приложения, а затем завершает работу. Если вы хотите создать потоковую передачу новых журналов, добавьте-f (--следовать):  

```
az spring-cloud app log tail -n auth-service -f
``` 
Чтобы проверить все поддерживаемые параметры ведения журнала, выполните следующие действия.
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>Дальнейшие действия

* [Анализ журналов и метрик с помощью параметров диагностики](./diagnostic-services.md)

 





