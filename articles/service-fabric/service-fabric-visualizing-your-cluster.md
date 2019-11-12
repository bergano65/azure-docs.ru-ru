---
title: Визуализация кластера с помощью Azure Service Fabric Explorer | Документация Майкрософт
description: Service Fabric Explorer — это приложение для изучения облачных приложений и узлов в кластере Microsoft Azure Service Fabric и управления ими.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 9b873b5a68979b8225c44c32e0b9494408e35ac1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927181"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Визуализация кластера с помощью Service Fabric Explorer

Service Fabric Explorer (SFX) — это инструмент с открытым кодом, предназначенный для проверки кластеров Azure Service Fabric и управления ими. Service Fabric Explorer — это классическое приложение для Windows, macOS и Linux.

## <a name="service-fabric-explorer-download"></a>Скачивание Service Fabric Explorer

Используйте следующие ссылки для скачивания Service Fabric Explorer в виде классического приложения:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Классическая версия Service Fabric Explorer может иметь больше или меньше функций по сравнению с функциями, поддерживаемыми кластером. Можно выполнить откат до версии Service Fabric Explorer, развернутой в кластере, чтобы обеспечить полную совместимость функций.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Выполнение Service Fabric Explorer из кластера

Service Fabric Explorer также размещается в конечной точке управления HTTP кластера Service Fabric. Чтобы запустить SFX в веб-браузере, перейдите к конечной точке управления HTTP кластера из любого браузера, например https:\//Клустерфкдн: 19080.

Для конфигурации рабочей станции разработки можно запустить Service Fabric Explorer на локальном кластере, перейдя по адресу https://localhost:19080/Explorer. Подготовка среды разработки описывается в [этой статье](service-fabric-get-started.md).

> [!NOTE]
> Если ваш кластер защищен с помощью самозаверяющего сертификата, в веб-браузере отобразится сообщение об ошибке This site is not secure (Этот веб-сайт не защищен). Вы можете просто продолжить работу в большинстве современных веб-браузерах, переопределив предупреждение. В рабочей среде кластер должен быть защищен с помощью общих имен и сертификата, выданного центром сертификации. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Подключение к кластеру Service Fabric
Для подключения к кластеру Service Fabric требуется его конечная точка управления (полное доменное имя или IP-адрес) и порт конечной точки управления HTTP (по умолчанию — 19080). Например, HTTPS\://mysfcluster.westus.cloudapp.azure.com:19080. Используйте флажок "Подключение к localhost", чтобы подключиться к локальному кластеру на рабочей станции.

### <a name="connect-to-a-secure-cluster"></a>Подключение к защищенному кластеру
Вы можете управлять клиентским доступом к кластеру Service Fabric с помощью сертификатов или Azure Active Directory (AAD).

При попытке подключения к защищенному кластеру в зависимости от конфигурации кластера потребуется предоставить сертификат клиента или выполнить вход с помощью AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Основные сведения о структуре обозревателя Service Fabric
Перейти к обозревателю Service Fabric можно с помощью дерева в левой части окна. В корне дерева на панели мониторинга кластера представлены общие сведения о кластере, включая общие сведения о приложении и работоспособности узла кластера.

![Панель мониторинга кластера обозревателя Service Fabric][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Просмотр макета кластера
Узлы в кластере Service Fabric размещаются в двухмерной сетке доменов сбоя и обновления, что обеспечивает доступность приложений при сбоях оборудования и обновлении приложений. План текущего кластера можно просмотреть на схеме кластера.

![Схема кластера в обозревателе Service Fabric][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Просмотр приложений и услуг
Кластер содержит два поддерева: одно для приложений и другое для узлов.

Представление приложений позволяет перемещаться по логической иерархии Service Fabric: приложениям, службам, разделам и репликам.

В примере ниже приложение **MyApp** состоит из двух служб: **MyStatefulService** и **WebSvcService**. Так как **MyStatefulService** — служба с отслеживанием состояния, она включает раздел с одной основной и двумя вторичными репликами. Напротив, WebSvcService — служба без сохранения состояния и содержит единственный экземпляр.

![Представление приложений обозревателя Service Fabric][sfx-application-tree]

На каждом уровне дерева на основной панели отображаются нужные сведения об элементе. Например, для конкретной службы вы увидите состояние работоспособности и версию.

![Панель основных сведений обозревателя Service Fabric][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Просмотр узлов кластера
В представлении "Узлы" отображается физическая структура кластера. Для каждого узла можно просмотреть, какие приложения были развернуты на этом узле и какие реплики запущены в настоящее время.

## <a name="actions"></a>Действия
Обозреватель Service Fabric позволяет быстро выполнять действия с узлами, приложениями и службами в кластере.

Например, чтобы удалить экземпляр приложения, выберите приложение из дерева в левой части и выберите **Действия** > **Удалить приложение**.

![Удаление приложения в обозревателе Service Fabric][sfx-delete-application]

> [!TIP]
> Те же действия можно выполнить, щелкнув знак многоточия (...) рядом с каждым элементом.
>
> Каждое действие, которое может быть выполнено с помощью обозревателя Service Fabric, также можно выполнить с помощью PowerShell или API REST, дающего выполнить автоматизацию.
>
>

Также можно использовать Service Fabric Explorer для создания экземпляров приложения для определенного типа и версии приложения. В представлении в виде дерева выберите тип приложения, затем в области справа щелкните ссылку **Create app instance** (Создать экземпляр приложения) рядом с необходимой версией.

![Создание экземпляра приложения в Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer не поддерживает параметры при создании экземпляров приложения. Экземпляры приложения используют значения параметров по умолчанию.
>
>

## <a name="event-store"></a>EventStore
EventStore — это функция, предлагаемая этой платформой. Она предоставляет события платформы Service Fabric, доступные в Service Fabric Explorer, а также через REST API. Вы можете просмотреть моментальный снимок происходящих действий в кластере для каждой сущности, например для узла, службы или приложения, а также отправить запрос с учетом времени возникновения события. Вы также можете получить дополнительные сведения об EventStore, ознакомившись со статьей [Общие сведения о службе EventStore](service-fabric-diagnostics-eventstore.md).   

![EventStore][sfx-eventstore]

>[!NOTE]
>Начиная с версии Service Fabric 6.4 EventStore не включена по умолчанию. Ее необходимо включить в шаблон Resource Manager.

>[!NOTE]
>Начиная с версии Service Fabric 6.4 интерфейсы API EventStore доступны только для кластеров Windows, работающих в Azure. Мы работаем над переносом этих функциональных возможностей в Linux, а также в изолированные кластеры.

## <a name="image-store-viewer"></a>Средство просмотра Хранилище образов
Средство просмотра хранилища образов — это функция, предлагаемая при использовании собственного Хранилище образов, которая позволяет просматривать текущее содержимое хранилища образов и получать сведения о файлах и папках вместе с удалением файлов и папок.

![Схема кластера в обозревателе Service Fabric][sfx-imagestore]

## <a name="backup-and-restore"></a>Архивация и восстановление
Service Fabric Explorer предлагает возможность взаимодействия с [резервным копированием и восстановлением](./service-fabric-reliable-services-backup-restore.md). Для просмотра функций резервного копирования и восстановления в SFX необходимо включить расширенный режим.

![Включить расширенный режим][0]
 
Возможны следующие операции:

* Создание, изменение и удаление политики архивации.
* Включение и отключение архивации для приложения, службы или секции.
* Приостановка и возобновление резервного копирования для приложения, службы или секции.
* Активация и контроль резервного копирования секции.
* Активация и отслеживание восстановления для секции.

Дополнительные сведения о службе резервного копирования и восстановления см. в [справочнике по REST API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Дополнительная информация
* [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Развертывание приложений Service Fabric с помощью PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png