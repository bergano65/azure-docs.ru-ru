---
title: Просмотр журналов контейнеров в Azure Service Fabric | Документация Майкрософт
description: Описывает способы просмотра журналов контейнеров в запущенных службах контейнеров Service Fabric с помощью Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: atsenthi
ms.openlocfilehash: 3fa40d794d02da08d29b6cac652edf493977f8e1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599731"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Просмотр журналов службы контейнеров Service Fabric
Azure Service Fabric — это оркестратор контейнеров, который поддерживает [Linux-контейнеры и Windows-контейнеры](service-fabric-containers-overview.md).  В этой статье описывается, как просматривать журналы контейнеров работающей службы контейнеров или неработающих контейнеров, чтобы проводить диагностику и устранение проблем.

## <a name="access-the-logs-of-a-running-container"></a>Доступ к журналам запущенного контейнера
Доступ к журналам контейнеров можно получить с помощью [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Откройте Service Fabric Explorer в веб-браузере из конечной точки управления кластера, перейдя на [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Журналы контейнеров расположены на узле кластера, на котором выполняется экземпляр службы контейнеров. Например, чтобы просмотреть журналы контейнера интерфейсной части [веб-приложения для голосования в Linux](service-fabric-quickstart-containers-linux.md). В представлении в виде дерева разверните ветку **Cluster**>**Applications**>**VotingType**>**fabric:/Voting/azurevotefront**.  Затем разверните раздел (здесь это d1aa737e-f22a-e347-be16-eec90be24bc1) и убедитесь, что контейнер запущен на узле кластера *_lnxvm_0*.

В представлении в виде дерева найдите пакет кода на узле *_lnxvm_0*, развернув ветку **Nodes**> **_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Code Packages**>**code**.  Чтобы просмотреть журналы контейнеров, выберите элемент **Журналы контейнеров**.

![Платформа Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Доступ к журналам неиспользуемых контейнеров или контейнеров со сбоем
Начиная с версии 6.2, вы можете получать журналы неиспользуемых контейнеров или контейнеров со сбоем с помощью команд [интерфейсов API REST](/rest/api/servicefabric/sfclient-index) или [интерфейса командной строки Service Fabric (SFCTL)](service-fabric-cli.md).

### <a name="set-container-retention-policy"></a>Настройка политики хранения контейнера
Чтобы упростить процесс диагностики сбоев при запуске контейнеров, Service Fabric (версии 6.1 и выше) поддерживает хранение контейнеров, работа которых завершилась или при запуске которых произошел сбой. Эту политику можно настроить в файле **ApplicationManifest.xml**, как показано в следующем фрагменте кода:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

В атрибуте **ContainersRetentionCount** указывается число контейнеров, которые следует сохранить после сбоя. Если указано отрицательное значение, будут сохраняться все контейнеры после сбоя. Если значение **ContainersRetentionCount** не указано, контейнеры не будут сохраняться. Атрибут **ContainersRetentionCount** также поддерживает параметры приложения, поэтому пользователи могут указывать разные значения для тестовых и рабочих кластеров. Чтобы предотвратить перемещение службы контейнеров на другие узлы, при использовании этих функций используйте ограничения на размещение, указав для службы контейнеров конкретный узел. Удаление всех контейнеров, сохраненных с помощью этой функции, выполняется вручную.

Параметр **RunInteractive** соответствует [флагам](https://docs.docker.com/engine/reference/commandline/run/#options) `--interactive` и `tty` в Docker. Если в файле манифеста этому параметра присвоено значение true, эти флаги используются для запуска контейнера.  

### <a name="rest"></a>REST
Выполните операцию [получения журналов контейнера, развернутого на узле](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode), чтобы получить журналы контейнеров со сбоем. Укажите имя узла, на котором был запущен контейнер, имя приложения, имя манифеста служб и имя пакета кода.  Укажите `&Previous=true`. Ответ будет содержать журналы неиспользуемого контейнера экземпляра пакета кода.

URI запроса будет выглядеть следующим образом:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Пример запроса:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

Текст ответа 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Выполните команду [sfctl service get-container-logs](service-fabric-sfctl-service.md), чтобы получить журналы контейнера со сбоем.  Укажите имя узла, на котором был запущен контейнер, имя приложения, имя манифеста служб и имя пакета кода. Укажите флаг `--previous`.  Ответ будет содержать журналы неиспользуемого контейнера экземпляра пакета кода.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Ответ:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Следующие шаги
- Ознакомьтесь с [Руководством по созданию контейнерных Linux-приложений](service-fabric-tutorial-create-container-images.md).
- Узнать больше о [Service Fabric и контейнерах](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
