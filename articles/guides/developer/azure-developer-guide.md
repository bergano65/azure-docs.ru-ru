---
title: Руководство по началу работы для разработчиков Azure | Документация Майкрософт
description: This article provides essential information for developers looking to get started using the Microsoft Azure platform for their development needs.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: c8ab954471e597cfea5c6f56cd45b2191aa73242
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424080"
---
# <a name="get-started-guide-for-azure-developers"></a>Руководство по началу работы для разработчиков Azure

## <a name="what-is-azure"></a>Что такое Azure?

Azure is a complete cloud platform that can host your existing applications and streamline new application development. Azure can even enhance on-premises applications. Azure integrates the cloud services that you need to develop, test, deploy, and manage your applications, all while taking advantage of the efficiencies of cloud computing.

Разместив свои приложения в Azure, можно начать с малого и незначительно масштабировать приложения по мере роста потребностей клиентов. Azure также предлагает надежность, необходимую для высокодоступных приложений, и даже отработку отказа между разными регионами. [Портал Azure](https://portal.azure.com) позволяет легко управлять всеми службами Azure. Для программного управления службами можно также использовать интерфейсы API служб и шаблоны.

Данное руководство содержит вводные сведения о платформе Azure для разработчиков приложений. В нем представлены рекомендации и указания, с помощью которых вы сможете начать разработку новых приложений в Azure или перенести существующие приложения в Azure.

## <a name="where-do-i-start"></a>С чего начать?

With all the services that Azure offers, it can be an intimidating task to figure out which services you need to support your solution architecture. В этом разделе описываются часто используемые разработчиками службы Azure. Список всех служб Azure см. в [документации Azure](../../index.md).

Во-первых, необходимо решить, как вы будете размещать приложение в Azure. Необходимо ли управлять всей инфраструктурой как виртуальной машиной? Можно использовать средства управления платформой, предоставляемые Azure? Возможно, требуется бессерверная платформа только для размещения выполнения кода?

Приложению требуется облачное хранилище, и Azure предоставляет несколько вариантов такого хранилища. Можно воспользоваться преимуществами корпоративной аутентификации Azure. Существуют также инструменты для облачной разработки и мониторинга, и большинство служб размещения обеспечивает интеграцию DevOps.

Теперь давайте рассмотрим несколько конкретных служб для ваших приложений, которые мы рекомендуем изучить.

### <a name="application-hosting"></a>Размещение приложений

Azure предоставляет несколько предложений облачных вычислений, предназначенных для выполнения приложений, которые позволяют не беспокоиться об элементах инфраструктуры. Можно легко масштабировать ресурсы по мере расширения использования приложения.

Azure предлагает службы, обеспечивающие потребности в разработке и размещении приложений. Azure предоставляет инфраструктуру как услугу (IaaS), обеспечивающую полный контроль над размещением приложений. Azure's Platform as a Service (PaaS) offerings provide the fully managed services needed to power your apps. There's even true serverless hosting in Azure where all you need to do is write your code.

![Варианты размещения приложений в Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Служба приложений Azure 

Если вам нужно как можно скорее публиковать веб-проекты, то вам следует рассмотреть службу приложений Azure. Служба приложений позволяет легко расширить возможности веб-приложений для поддержки мобильных клиентов и публиковать простые в использовании интерфейсы REST API. Эта платформа обеспечивает аутентификацию посредством поставщиков социальных сетей, автомасштабирование на основе трафика, тестирование в рабочей среде, а также непрерывное развертывание и развертывание на основе контейнера.

Вы можете создавать веб-приложения, серверные части мобильных приложений и приложения API.

Так как все три типа приложения используют общую среду выполнения службы приложений, вы можете разместить веб-сайт, включить поддержку мобильных клиентов и предоставить собственные интерфейсы API в Azure из одного проекта или решения. Дополнительные сведения о службе приложений см. в статье [Обзор веб-приложений](../../app-service/overview.md).

Служба приложений была разработана с ориентацией на DevOps. It supports various tools for publishing and continuous integration deployments. These tools include GitHub webhooks, Jenkins, Azure DevOps, TeamCity, and others.

Можно перенести существующие приложения в службу приложений с помощью [средства миграции в режиме онлайн](https://www.migratetoazure.net/).

> **When to use**: Use App Service when you’re migrating existing web applications to Azure, and when you need a fully-managed hosting platform for your web apps. Службу приложений можно также использовать, когда в приложении необходима поддержка мобильных клиентов или предоставление интерфейсов REST API.
> 
> **Приступая к работе**. Служба приложений позволяет легко создать и развернуть свое первое [веб-приложение](../../app-service/app-service-web-get-started-dotnet.md), [мобильное приложение](../../app-service-mobile/app-service-mobile-ios-get-started.md) или [приложение API](../../app-service/app-service-web-tutorial-rest-api.md).
> 
> **Попробуйте прямо сейчас**. Служба приложений позволяет подготовить временное приложение, чтобы опробовать возможности платформы без регистрации учетной записи Azure. Опробуйте функции платформы и [создайте приложение службы приложений Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Виртуальные машины Azure

Являясь поставщиком инфраструктуры как услуги (IaaS), Azure позволяет развернуть или перенести приложения на виртуальные машины Windows или Linux. Вместе с виртуальной сетью Azure служба "Виртуальные машины Azure" поддерживает развертывание виртуальных машин Windows или Linux в Azure. Использование виртуальных машин дает полный контроль над конфигурацией компьютера. При использовании виртуальных машин вы отвечаете за установку, настройку и обслуживание всего серверного программного обеспечения, а также за установку исправлений для операционной системы.

Благодаря уровню контроля, обеспечиваемому виртуальными машинами, в Azure можно запускать широкий диапазон серверных рабочих нагрузок, не подходящих для модели PaaS. К этим рабочим нагрузкам относятся серверы базы данных, Windows Server Active Directory и Microsoft SharePoint. Дополнительные сведения см. в документации по виртуальным машинам [Linux](/azure/virtual-machines/linux/) или [Windows](/azure/virtual-machines/windows/).

> **Когда следует использовать**. Используйте службу "Виртуальные машины", когда требуется полный контроль над инфраструктурой приложений или необходимо перенести рабочие нагрузки локальных приложений в Azure без внесения изменений.
> 
> **Приступая к работе**. Создайте [виртуальную машину Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) или [виртуальную машину Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) на портале Azure.

#### <a name="azure-functions-serverless"></a>Функции Azure (бессерверные)

Rather than worrying about building out and managing a whole application or the infrastructure to run your code, what if you could just write your code and have it run in response to events or on a schedule?  [Функции Azure](../../azure-functions/functions-overview.md) — бессерверное предложение, которое позволяет создавать только необходимый вам код. With Functions, you can trigger code execution with HTTP requests, webhooks, cloud service events, or on a schedule. Вы можете программировать на выбранном вами языке разработки, включая C\#, F\#, Node.js, Python и PHP. Благодаря выставлению счетов на основе использования вы платите только за время выполнения кода, и Azure масштабирует ресурсы по мере необходимости.

> **Когда следует использовать**. Используйте службу "Функции Azure", когда имеется код, активируемый другими службами Azure, веб-событиями либо активируемый по расписанию. Можно также использовать "Функции Azure", когда нет необходимости в дополнительных затратах на размещение всего проекта или если нужно платить только за время выполнения кода. Дополнительные сведения см. в статье [Общие сведения о Функциях Azure](../../azure-functions/functions-overview.md).
> 
> **Приступая к работе**. Изучите краткое руководство по службе "Функции Azure", чтобы [создать свою первую функцию](../../azure-functions/functions-create-first-azure-function.md) на портале.
> 
> **Попробуйте прямо сейчас**. Служба "Функции Azure" позволяет выполнять код без регистрации учетной записи Azure. Опробуйте ее возможности прямо сейчас и [создайте свою первую функцию Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric is a distributed systems platform. This platform makes it easy to build, package, deploy, and manage scalable and reliable microservices. It also provides comprehensive application management capabilities such as: 

* Подготовка
* Развертывание 
* Мониторинг
* Upgrading/patching
* Удаление 

Приложения, которые выполняются в общем пуле компьютеров, можно создавать с небольшими ресурсами и при необходимости масштабировать до сотен или тысяч компьютеров.

Service Fabric поддерживает веб-API благодаря использованию Open Web Interface для .NET (OWIN) и ASP.NET Core. Эта служба предоставляет пакеты SDK для создания служб в среде Linux с помощью .NET Core и Java. Дополнительные сведения о Service Fabric см. в [документации по Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Когда следует использовать**. Service Fabric будет хорошим выбором, если вы создаете новое или изменяете существующее приложение с намерением использовать архитектуру микрослужб. Используйте Service Fabric, если требуется больший контроль над базовой инфраструктурой или прямой доступ к ней.
> 
> **Приступая к работе**. [Создайте свое первое приложение Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Расширение возможностей приложений с помощью служб Azure

Along with application hosting, Azure provides service offerings that can enhance the functionality. Azure can also improve the development and maintenance of your applications, both in the cloud and on-premises.

#### <a name="hosted-storage-and-data-access"></a>Размещенное хранилище и доступ к данным

Most applications must store data, so however you decide to host your application in Azure, consider one or more of the following storage and data services.

- **Azure Cosmos DB**: A globally distributed, multi-model database service. This database enables you to elastically scale throughput and storage across any number of geographical regions with a comprehensive SLA. 
  
  > **Сценарии использования** — если для приложения требуются базы данных документов, таблиц или диаграмм, включая базы данных MongoDB, с несколькими четко определенными и согласованными моделями. 
  > 
  > **Начало работы** — [создание веб-приложения Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Если вы разработчик MongoDB, см. статью о [создании веб-приложения MongoDB с помощью Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Служба хранилища Azure**. Обеспечивает надежное высокодоступное хранилище для больших двоичных объектов, очередей, файлов и других типов нереляционных данных. Служба хранилища является платформой хранения данных для виртуальных машин.

  > **Когда следует использовать**. Если приложение хранит нереляционные данные, такие как пары "ключ-значение" (таблицы), большие двоичные объекты, файловые ресурсы или сообщения (очереди).
  > 
  > **Приступая к работе**. Выберите один из этих типов: хранилище [BLOB-объектов](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [таблиц](../../cosmos-db/table-storage-how-to-use-dotnet.md), [очередей](../../storage/queues/storage-dotnet-how-to-use-queues.md) или [файлов](../../storage/files/storage-dotnet-how-to-use-files.md).

- **База данных SQL Azure**. Версия ядра Microsoft SQL Server на основе Azure для хранения реляционных табличных данных в облаке. База данных SQL обеспечивает прогнозируемую производительность, масштабируемость без простоев, непрерывность бизнес-процессов и защиту данных.

  > **Когда следует использовать**. Если вашему приложению требуется хранилище данных, обеспечивающее целостность данных, а также поддержка транзакций и TSQL-запросов.
  > 
  > **Приступая к работе**. [Создайте базу данных SQL за несколько минут с помощью портала Azure](../../sql-database/sql-database-get-started.md).


Можно использовать [фабрику данных Azure](../../data-factory/introduction.md) для перемещения существующих локальных данных в Azure. Если вы не готовы переместить данные в облако, то с помощью [гибридных подключений](../../biztalk-services/integration-hybrid-connection-overview.md) в службах BizTalk сможете подключить приложение, размещенное в службе приложений, к локальным ресурсам. Вы сможете также подключаться к службам хранения и обработки данных Azure из локальных приложений.

#### <a name="docker-support"></a>Поддержка Docker

Контейнеры Docker, представляющие собой разновидность виртуализации ОС, позволяют развертывать приложения более эффективным и предсказуемым способом. Контейнерное приложение работает в рабочей среде таким же образом, как и в системах разработки и тестирования. Контейнерами можно управлять с помощью стандартных инструментов Docker. Для развертывания контейнерных приложений и управления ими в Azure можно использовать имеющиеся навыки и популярные инструменты с открытым кодом.

В Azure доступно несколько способов использования контейнеров в приложениях.

- **Расширение виртуальной машины Docker Azure**. Позволяет настроить виртуальную машину в качестве узла Docker с помощью инструментов Docker.

  > **Когда следует использовать**. Если вы хотите создавать согласованные развертывания на основе контейнера для приложений на виртуальной машине или намереваетесь использовать [Docker Compose](https://docs.docker.com/compose/overview/).
  > 
  > **Приступая к работе**. [Создайте среду Docker в Azure с помощью расширения виртуальной машины Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Служба контейнеров Azure**. Позволяет создать и настроить кластер виртуальных машин, предварительно настроенных для запуска контейнерных приложений, а также управлять этим кластером. Дополнительные сведения о службе контейнеров см. в разделе [Introduction to Azure Container Service for Kubernetes](../../container-service/container-service-intro.md) (Введение в службу контейнеров Azure для Kubernetes).

  > **Когда следует использовать**. Если вам нужно создавать готовые к работе, масштабируемые среды с дополнительными средствами планирования и управления или если вы развертываете кластер Docker Swarm.
  > 
  > **Приступая к работе**. [Разверните кластер службы контейнеров](../../container-service/dcos-swarm/container-service-deployment.md).

- **Виртуальная машина Docker**. Позволяет установить подсистему Docker и управлять ей на виртуальных узлах с помощью команд виртуальной машины Docker.

  >**Когда следует использовать**. Когда требуется быстро прототипировать приложение путем создания отдельного узла Docker.

- **Пользовательский образ Docker для службы приложений**. Позволяет использовать контейнеры Docker из реестра контейнеров или контейнер клиента при развертывании веб-приложения на платформе Linux.

  > **Когда следует использовать**. При развертывании веб-приложения на платформе Linux в образ Docker.
  > 
  > **Приступая к работе**. [Используйте пользовательский образ Docker для службы приложений на платформе Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Очень важно не только знать, кто использует ваши приложения, но и предотвращать несанкционированный доступ к ресурсам. Azure предоставляет несколько способов аутентификации клиентов приложения.

- **Azure Active Directory (Azure AD)** . Мультитенантная облачная служба управления удостоверениями и доступом корпорации Майкрософт. Вы можете добавить в свои приложения единый вход, интегрировав их с Azure AD. Можно получить доступ к свойствам каталога напрямую с помощью API Azure AD Graph или с помощью API Microsoft Graph. Можно интегрировать Azure AD с поддержкой инфраструктуры авторизации OAuth2.0 и Open ID Connect, используя собственные конечные точки HTTP/REST и многоплатформенные библиотеки аутентификации Azure AD.

  > **Когда следует использовать**. Если вам нужно обеспечить единый вход, работать с данными на основе Graph или аутентифицировать пользователей на основе домена.
  > 
  > **Приступая к работе**. Дополнительные сведения см. в [руководстве разработчика для Azure Active Directory](../../active-directory/develop/v1-overview.md).

- **Аутентификация в службе приложений**. При выборе службы приложений для размещения приложения можно также получить встроенную поддержку аутентификации с помощью Azure AD, а также поставщиков удостоверений социальных сетей, включая Facebook, Google, учетные записи Майкрософт и Twitter.

  > **Когда следует использовать**. Если вы хотите обеспечить аутентификацию в приложении службы приложений с помощью Azure AD, поставщиков удостоверений социальных сетей или обеих этих служб.
  > 
  > **Приступая к работе**. Дополнительные сведения об аутентификации в службе приложений см. в разделе [Проверка подлинности и авторизация в службе приложений Azure](../../app-service/overview-authentication-authorization.md).

Дополнительные сведения о рекомендациях по безопасности в Azure см. в разделе [Рекомендации и шаблоны для обеспечения безопасности в Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Мониторинг

With your application up and running in Azure, you need to monitor performance, watch for issues, and see how customers are using your app. Azure предоставляет несколько вариантов мониторинга.

-   **Visual Studio Application Insights**. Расширяемая служба аналитики, размещенная в Azure, которая интегрируется с Visual Studio и позволяет отслеживать работающие веб-приложения It gives you the data that you need to improve the performance and usability of your apps continuously. This improvement occurs whether you host your applications on Azure or not.

    >**Приступая к работе**. Изучите [руководство по Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: A service that helps you to visualize, query, route, archive, and act on the metrics and logs that you generate with your Azure infrastructure and resources. Monitor is a single source for monitoring Azure resources and provides the data views that you see in the Azure portal.
 
    >**Приступая к работе**. [Начните работу с Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Интеграция DevOps

Azure интегрируется с большинством популярных инструментов DevOps, используемых при подготовке виртуальных машин или публикации веб-приложений с непрерывной интеграцией. You can work with the tools that you already have and maximize your existing experience with support for tools like: 

* Jenkins 
* GitHub 
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Приступая к работе**. Чтобы просмотреть варианты использования DevOps для приложения службы приложений, ознакомьтесь с разделом [Непрерывное развертывание в службе приложений Azure](../../app-service/deploy-continuous-deployment.md).
> 
> **Попробуйте прямо сейчас**. [Опробуйте несколько интегрируемых компонентов DevOps](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Регионы Azure

Azure является глобальной облачной платформой, которая общедоступна во многих регионах по всему миру. When you provision a service, application, or VM in Azure, you're asked to select a region. This region represents a specific datacenter where your application runs or where your data is stored. Эти регионы соответствуют определенным расположениям, которые публикуются на странице [Регионы Azure](https://azure.microsoft.com/regions/).

### <a name="choose-the-best-region-for-your-application-and-data"></a>Выбор наилучшего региона для приложений и данных

Одним из преимуществ использования Azure является то, что можно развертывать приложения в разных центрах обработки данных по всему миру. Выбранный регион может повлиять на производительность приложения. Например, лучше выбрать регион, расположенный ближе к большинству клиентов, чтобы уменьшить задержки при передаче сетевых запросов. You might also want to select your region to meet the legal requirements for distributing your app in certain countries/regions. Рекомендуется всегда хранить данные приложения в том же центре обработки данных, в котором размещается ваше приложение, или в центре обработки данных, находящемся к нему как можно ближе.

### <a name="multi-region-apps"></a>Приложения с поддержкой нескольких регионов

Хотя и крайне маловероятно, но возможно отключение от сети всего центра обработки данных из-за таких событий, как стихийное бедствие или сбои в сети Интернет. Рекомендуется размещать критически важные бизнес-приложения в более чем одном центре обработки данных, чтобы обеспечить их максимальную доступность. Размещение в нескольких регионах может также снизить задержку для глобальных пользователей и обеспечивает дополнительную гибкость при обновлении приложений.

Некоторые службы, например служба "Виртуальные машины" и службы приложений, используют [диспетчер трафика Azure](../../traffic-manager/traffic-manager-overview.md) для поддержки нескольких регионов с отработкой отказа между регионами, чтобы обеспечить высокий уровень доступности корпоративных приложений. С примером можно ознакомиться в разделе [Эталонная архитектура Azure. Выполнение веб-приложения в нескольких регионах](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Когда следует использовать**. При наличии корпоративных и высокодоступных приложений, которые получают преимущества от репликации и отработки отказа.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Как управлять приложениями и проектами?

Azure предоставляет широкий набор функций для создания ресурсов, приложений и проектов Azure и управления ими как программно, так и с помощью [портала Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Интерфейсы командной строки и PowerShell

Azure provides two ways to manage your applications and services from the command line. You can use tools like Bash, Terminal, the command prompt, or your command-line tool of choice. Usually, you can do the same tasks from the command line as in the Azure portal—such as creating and configuring virtual machines, virtual networks, web apps, and other services.

-   [Интерфейс командной строки Azure (Azure CLI)](../../xplat-cli-install.md). Позволяет подключиться к подписке Azure и программировать различные задачи с ресурсами Azure из командной строки.

-   [Azure PowerShell](../../powershell-install-configure.md). Предоставляет набор модулей с командлетами, которые позволяют управлять ресурсами Azure с помощью Windows PowerShell.

### <a name="azure-portal"></a>портала Azure

The [Azure portal](https://portal.azure.com) is a web-based application. You can use the Azure portal to create, manage, and remove Azure resources and services. Сюда входят:

* A configurable dashboard
* Azure resource management tools
* Access to subscription settings and billing information. Дополнительные сведения см. в [обзоре портала Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>API-интерфейсы REST

В основе платформы Azure лежит набор интерфейсов REST API, которые поддерживают пользовательский интерфейс портала Azure. Большинство этих интерфейсов REST API поддерживается также для того, чтобы дать вам возможность программно подготавливать ресурсы и приложения Azure и управлять ими с любого устройства с подключением к Интернету. Полный набор документации по REST API приведен в [справочнике по пакету SDK REST для Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>API-интерфейсы

Along with REST APIs, many Azure services also let you programmatically manage resources from your applications by using platform-specific Azure SDKs, including SDKs for the following development platforms:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [GO](https://docs.microsoft.com/azure/go)

Такие службы, как [мобильные приложения](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) и [службы мультимедиа Azure](../../media-services/previous/media-services-dotnet-how-to-use.md), предоставляют клиентские пакеты SDK, чтобы обеспечить доступ к этим службам из клиентских веб-приложений и мобильных приложений.

### <a name="azure-resource-manager"></a>Диспетчер ресурсов Azure 
    
Running your app on Azure likely involves working with multiple Azure services. These services follow the same life cycle and can be thought of as a logical unit. Например, веб-приложение может использовать службы "Веб-приложения", "База данных SQL", службу хранилища, кэш Redis для Azure и сеть доставки содержимого Azure. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) позволяет работать с ресурсами в приложении как с группой. Вы можете развернуть, обновить или удалить все ресурсы одной скоординированной операцией.

Along with logically grouping and managing related resources, Azure Resource Manager includes deployment capabilities that let you customize the deployment and configuration of related resources. For example, you can use Resource Manager deploy and configure an application. This application can consist of multiple virtual machines, a load balancer, and an Azure SQL database as a single unit.

Для разработки таких развертываний используется шаблон Azure Resource Manager, представляющий собой документ в формате JSON. Шаблоны позволяют определить развертывание и управлять приложениями с помощью декларативных шаблонов, а не сценариев. Шаблоны можно использовать для разных сред, в том числе для тестовой, промежуточной и рабочей. For example, you can use templates to add a button to a GitHub repo that deploys the code in the repo to a set of Azure services with a single click.

> **Когда следует использовать**. Используйте шаблоны Resource Manager, когда требуется развертывание приложения на основе шаблона, которым можно управлять программно с помощью интерфейсов REST API, Azure CLI и Azure PowerShell.
> 
> **Приступая к работе**. Чтобы приступить к использованию шаблонов, ознакомьтесь с разделом [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Сведения об учетных записях, подписках и выставлении счетов

Разработчикам хочется сразу углубиться в программирование и как можно скорее приступить к работе, чтобы запустить приложение. Безусловно, мы хотим, чтобы вам было как можно проще приступить к работе в Azure. Для этого Azure предлагает [бесплатную пробную версию](https://azure.microsoft.com/free/). Некоторые службы, например [служба приложений Azure](https://tryappservice.azure.com/), даже предлагают бесплатные пробные функции, для использования которых не требуется создавать учетную запись. As fun as it is to dive into coding and deploying your application to Azure, it's also important to take some time to understand how Azure works. Specifically,  you should understand how it works from a standpoint of user accounts, subscriptions, and billing.

### <a name="what-is-an-azure-account"></a>Что такое учетная запись Azure?

To create or work with an Azure subscription, you must have an Azure account. An Azure account is simply an identity in Azure AD or in a directory, such as a work or school organization, that Azure AD trusts. Если вы не относитесь к такой организации, всегда можно создать подписку с помощью учетной записи Майкрософт, которая является доверенной для Azure AD. Дополнительные сведения об интеграции локального каталога Windows Server Active Directory с Azure AD см. в разделе [Интеграция локальных каталогов с Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Между каждой подпиской Azure и экземпляром Azure AD установлено отношение доверия. Это означает, что она доверяет каталогу проверять подлинность пользователей, служб и устройств. Несколько подписок могут доверять одному и тому же каталогу, но одна конкретная подписка доверяет только одному каталогу. Дополнительные сведения см. в разделе [Связь между подписками Azure и службой Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

As well as defining individual Azure account identities, also called *users*, you can define *groups* in Azure AD. Создание групп пользователей — хороший способ контролировать доступ к ресурсам в подписке с помощью управления доступом на основе ролей (RBAC). Чтобы узнать, как создавать группы, ознакомьтесь с разделом [Создание группы и добавление в нее пользователей в Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Создавать группы и управлять ими можно также [с помощью PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Управление подписками

Подписка — это логическая группа служб Azure, связанная с учетной записью Azure. Одна учетная запись Azure может содержать несколько подписок. Выставление счетов за службы Azure выполняется по подписке. Список доступных подписок, упорядоченных по типу, приведен в разделе [Сведения о предложении Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Azure subscriptions have an Account Administrator who has full control over the subscription. They also have a Service Administrator who has control over all services in the subscription. Дополнительные сведения о классических администраторах подписки см. в статье [Добавление или изменение администраторов подписки Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Individual accounts can be granted detailed control of Azure resources using [role-based access control (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Группы ресурсов

Подготовка новых служб Azure осуществляется в заданной подписке. Отдельные службы Azure, которые также называются ресурсами, создаются в контексте группы ресурсов. Группы ресурсов упрощают развертывание ресурсов приложения и управление ими. Все ресурсы для приложения, с которыми вы собираетесь работать как с единым целым, следует поместить в группу ресурсов. Вы можете перемещать ресурсы между группами ресурсов и даже между разными подписками. Чтобы узнать о том, как перемещать ресурсы, ознакомьтесь с разделом [Перемещение ресурсов в новую группу ресурсов или подписку](../../resource-group-move-resources.md).

Обозреватель ресурсов Azure — отличный инструмент для визуализации ресурсов, уже созданных в подписке. Чтобы узнать больше, ознакомьтесь с [использованием обозревателя ресурсов Azure для просмотра и изменения ресурсов](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Предоставление доступа к ресурсам

When you allow access to Azure resources, it’s always a best practice to provide users with the least privilege that’s required to do a given task.

- **Управление доступом на основе ролей (RBAC)** . В Azure вы можете предоставить учетным записям пользователей (участникам) доступ в указанной области. Это может быть подписка, группа ресурсов или отдельные ресурсы. RBAC lets you deploy resources into a resource group and grant permissions to a specific user or group. It also lets you limit access to only the resources that belong to the target resource group. Можно предоставить доступ к отдельному ресурсу, например виртуальной машине или виртуальной сети. Чтобы предоставить доступ, можно назначить роль пользователю, группе или субъекту-службе. Существует множество предопределенных ролей. Кроме того, можно определить собственные настраиваемые роли. См. дополнительные сведения об [управлении доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md).

  > **Когда следует использовать**. Если требуется точное управление доступом для пользователей и групп или если нужно сделать пользователя владельцем подписки.
  > 
  > **Приступая к работе**. Дополнительные сведения см. в статье [Управление доступом с помощью RBAC и портала Azure](../../role-based-access-control/role-assignments-portal.md).

- **Service principal objects**: Along with providing access to user principals and groups, you can grant the same access to a service principal.

  > **Когда следует использовать**. При программном управлении ресурсами Azure или предоставлении доступа приложениям. Дополнительные сведения см. в разделе [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Теги

Azure Resource Manager дает возможность назначать настраиваемые теги для отдельных ресурсов. Теги, представляющие собой пары "ключ-значение", могут быть полезными, когда требуется организовать ресурсы для выставления счетов или мониторинга. С помощью тегов можно отслеживать ресурсы в нескольких группах ресурсов. You can assign tags the following ways:

* In the portal 
* In the Azure Resource Manager template 
* Использование REST API
* Использование Azure CLI
* Использование PowerShell 

Каждому ресурсу можно назначить несколько тегов. Чтобы узнать больше, ознакомьтесь также с разделом [Использование тегов для организации ресурсов в Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Выставление счетов

При переходе с локальных вычислительных служб на службы, размещенные в облаке, значительной проблемой являются отслеживание и оценка использования служб и связанных затрат. It’s important to estimate what new resources cost to run on a monthly basis. You can also project how the billing looks for a given month based on the current spending.

#### <a name="get-resource-usage-data"></a>Получение данных об использовании ресурсов

Azure предоставляет набор интерфейсов REST API выставления счетов, которые предоставляют доступ к информации о потреблении ресурсов и к метаданным для подписки Azure. Эти API выставления счетов дают возможность лучше прогнозировать и контролировать затраты на Azure. You can track and analyze spending in hourly increments and create spending alerts. You can also predict future billing based on current usage trends.

>**Приступая к работе**. Дополнительные сведения об использовании интерфейсов API выставления счетов см. в разделе [Использование API выставления счетов Azure для программного получения ценных сведений об использовании Azure](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Прогнозирование будущих затрат

Although it's challenging to estimate costs ahead of time, Azure has tools that can help. It has a [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to help estimate the cost of deployed resources. You can also use the Billing resources in the portal and the Billing REST APIs to estimate future costs, based on current consumption.

>**Приступая к работе**. См. раздел [Использование API выставления счетов Azure для программного получения ценных сведений об использовании Azure](../../billing-usage-rate-card-overview.md).
