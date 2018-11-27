---
title: Развертывание приложения Service Fabric с непрерывной интеграцией (Azure DevOps Services) в Azure | Документация Майкрософт
description: В этом руководстве представлены общие сведения о том, как настроить непрерывную интеграцию и развертывание для приложения Service Fabric с помощью Azure DevOps Services.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/15/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 5d53250ebdc14b7b6631e2f419b5b24ac98f3038
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853748"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Руководство. Развертывание приложения с непрерывной интеграцией и непрерывным развертыванием в кластере Service Fabric

Это четвертое руководство из цикла. В нем описано, как настроить непрерывные интеграцию и развертывание для приложения Azure Service Fabric с помощью Azure DevOps Services.  Вам потребуется приложение Service Fabric. В качестве примера используется приложение, созданное в разделе [Создание приложения .NET](service-fabric-tutorial-create-dotnet-app.md).

В третьей части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Добавление проекта в систему управления версиями
> * Создание конвейера сборки в Azure DevOps
> * Создание конвейера выпуска в Azure DevOps
> * Автоматическое развертывание и обновление приложения

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * [Создание приложения .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md).
> * [Развертывание приложения в удаленном кластере](service-fabric-tutorial-deploy-app-to-party-cluster.md).
> * [Добавление конечной точки HTTPS к интерфейсной службе ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Настройка CI/CD с помощью Azure Pipelines
> * [Настройка мониторинга и диагностики приложения](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Установите Visual Studio 2017](https://www.visualstudio.com/), а также рабочие нагрузки **разработка Azure** и **ASP.NET и веб-разработка**.
* [Установите пакет SDK для Service Fabric](service-fabric-get-started.md)
* Создайте кластер Service Fabric с Windows, например с помощью [этого руководства](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
* Создание [организации Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student).

## <a name="download-the-voting-sample-application"></a>Скачивание примера приложения для голосования

Если вы не создавали пример приложения для голосования [в первой части этой серии руководств](service-fabric-tutorial-create-dotnet-app.md), вы можете скачать его. В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Подготовка профиля публикации

После [создания приложения](service-fabric-tutorial-create-dotnet-app.md) и [развертывания приложения в Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md) вы можете настроить непрерывную интеграцию.  Прежде всего подготовьте в приложении профиль публикации, который будет использоваться в процессе развертывания под управлением Azure DevOps.  Профиль публикации следует настроить для целевого кластера, который был создан ранее.  Запустите Visual Studio и откройте существующий проект приложения Service Fabric.  Щелкните правой кнопкой мыши приложение в **обозревателе решений** и выберите **Опубликовать...**

Выберите в проекте приложения целевой профиль, который будет использоваться для рабочего процесса непрерывной интеграции, например "Облако".  Укажите конечную точку подключения кластера.  Установите флажок **Обновлять приложение**, чтобы ваше приложение обновлялось при каждом развертывании в Azure DevOps.  Нажмите ссылку **Сохранить**, чтобы сохранить параметры профиля публикации, а затем нажмите кнопку **Отменить**, чтобы закрыть диалоговое окно.

![Принудительная отправка профиля][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Публикация решения Visual Studio в новом репозитории Azure DevOps Git

Поместите исходные файлы приложения в проект Azure DevOps, чтобы создавать сборки.

Создайте новый локальный репозиторий Git, выбрав **Добавить в систему управления версиями** -> **Git** в строке состояния в правом нижнем углу Visual Studio.

В представлении **Принудительная отправка** в **Team Explorer** выберите **Опубликовать репозиторий Git** в разделе **Принудительная отправка в Azure DevOps**.

![Принудительная отправка репозитория Git][push-git-repo]

Проверьте адрес электронной почты и выберите учетную запись в раскрывающемся списке **Домен Azure DevOps**. Введите имя репозитория и выберите **Опубликовать репозиторий**.

![Принудительная отправка репозитория Git][publish-code]

При публикации репозитория в вашей учетной записи создается новый проект с тем же именем, что и у локального репозитория. Чтобы создать репозиторий в существующем проекте, щелкните **Расширенный** рядом с **именем репозитория** и выберите нужный проект. Код можно просматривать в Интернете, выбрав **Просмотреть на веб-сайте**.

## <a name="configure-continuous-delivery-with-azure-devops"></a>Настройка непрерывной поставки с помощью Azure DevOps

Конвейер сборки Azure DevOps описывает рабочий процесс, включающий набор последовательно выполняемых шагов сборки. Создайте конвейер сборки, который создает пакет приложения Service Fabric и другие артефакты для развертывания в кластере Service Fabric. Получите дополнительные сведения [о конвейерах сборки Azure DevOps](https://www.visualstudio.com/docs/build/define/create). 

Конвейер выпуска Azure DevOps описывает рабочий процесс, развертывающий пакет приложения в кластере. При совместном использовании конвейер сборки и конвейер выпуска выполняют весь рабочий процесс от получения файлов с исходным кодом до запуска приложения в кластере. Получите дополнительные сведения [о конвейерах выпуска Azure DevOps](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-pipeline"></a>Создание конвейера сборки

Откройте веб-браузер и перейдите к новому проекту по адресу: [https://&lt;учетная_запись&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Перейдите на вкладку **Контейнеры**, выберите **Сборки** и щелкните **Создать конвейер**.

![Новый конвейер][new-pipeline]

Выберите **Azure Repos Git** как источник, командный проект **Voting**, репозиторий **Voting** и ветвь **master** по умолчанию или сборки, выполняемые вручную или по расписанию.  Затем нажмите кнопку **Продолжить**.

![Выбор репозитория][select-repo]

В области **Выбор шаблона** выберите шаблон **Приложение Azure Service Fabric** и нажмите кнопку **Применить**.

![Выбор шаблона сборки][select-build-template]

Выберите **Задачи** и введите "Размещается в VS2017" в качестве значения параметра **Очередь агента**.

![Выбор задач][save-and-queue]

В разделе **Триггеры** включите непрерывную интеграцию, установив флажок **Включить непрерывную интеграцию**. В **фильтрах ветвей** ветви **master** будет по умолчанию назначена **спецификация ветви**. Щелкните **Сохранить и поместить в очередь**, чтобы выполнить сборку вручную.

![Выбор триггеров][save-and-queue2]

Сборки также активируются после принудительного запуска или возврата. Чтобы проверить ход сборки, перейдите на вкладку **Сборки**.  Убедившись, что сборка запускается успешно, определите конвейер выпуска, который развертывает приложение в кластер.

### <a name="create-a-release-pipeline"></a>Создание конвейера выпуска

Перейдите на вкладку **Контейнеры**, выберите **Выпуски** и щелкните **+Создать конвейер**.  В области **Выбор шаблона** выберите шаблон **Развертывание Azure Service Fabric** в списке и нажмите кнопку **Применить**.

![Выбор шаблона выпуска][select-release-template]

Выберите **Задачи**->**Среда 1**, а затем щелкните **+ Создать**, чтобы добавить новое подключение кластера.

![Добавление подключения к кластеру][add-cluster-connection]

В представлении **Add new Service Fabric Connection** (Добавление нового подключения Service Fabric) выберите проверку подлинности **на основе сертификатов** или **Azure Active Directory**.  Укажите имя подключения "mysftestcluster" и конечную точку кластера "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (или конечную точку кластера, куда выполняется развертывание).

Для проверки подлинности на основе сертификатов добавьте **отпечаток сертификата сервера**, используемого для создания кластера.  В поле **Сертификат клиента** добавьте файла сертификата клиента в кодировке Base-64. Сведения о том, как получить представление сертификата в кодировке Base-64, см. во вплывающем окне в этом поле. Добавьте также **пароль** для сертификата.  При отсутствии отдельного клиентского сертификата можно использовать сертификат кластера или сервера.

Для учетных данных Azure Active Directory добавьте **отпечаток сертификата сервера**, используемый для создания кластера, и учетные данные, которые необходимо использовать для подключения к кластеру, в полях **Имя пользователя** и **Пароль**.

Нажмите кнопку **Добавить**, чтобы сохранить подключение кластера.

Затем добавьте в конвейер артефакт сборки, чтобы для конвейера выпуска были доступны выходные данные сборки. Выберите **Конвейер** и **Артефакты**->**+ Добавить**.  В поле **Источник (определение сборки)** выберите созданный ранее конвейер сборки.  Щелкните **Добавить**, чтобы сохранить артефакт сборки.

![Добавление артефакта][add-artifact]

Включите триггер непрерывного развертывания, чтобы выпуск создавался автоматически после завершения сборки. Щелкните значок молнии в артефакте, включите триггер и щелкните **Сохранить**, чтобы сохранить определение конвейера.

![Включение триггера][enable-trigger]

Чтобы создать выпуск вручную, последовательно выберите **+Выпуск** -> **Создать выпуск** -> **Создать**. Ход создания можно отслеживать на вкладке **Выпуски**.

Убедитесь, что развертывание выполнено успешно и приложение выполняется в кластере.  Откройте браузер и перейдите по ссылке [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Обратите внимание на версию приложения, в данном случае "1.0.0.20170616.3".

## <a name="commit-and-push-changes-trigger-a-release"></a>Фиксация и отправка изменений, создание выпуска

Чтобы проверить работу конвейера непрерывной интеграции, отправьте некоторые изменения кода в Azure DevOps.

При написании кода в Visual Studio изменения отслеживаются автоматически. Зафиксировать изменения в локальном репозитории Git можно, щелкнув значок ожидающих изменений (![Ожидает][pending]) в строке состояния в нижнем правом углу.

В представлении **Изменения** в Team Explorer добавьте сообщение с описанием обновления и зафиксируйте изменения.

![Фиксация всех изменений][changes]

Щелкните значок неопубликованных изменений в строке состояния (![Неопубликованные изменения][unpublished-changes]) или в представлении "Синхронизация" в Team Explorer. Выберите **Отправить изменения**, чтобы обновить код в Azure DevOps Services/TFS.

![Отправка изменений][push]

При отправке изменений в Azure DevOps автоматически запускается сборка.  После успешного создания конвейера сборки автоматически создается выпуск и начинается обновление приложения в кластере.

Чтобы проверить ход сборки, перейдите на вкладку **Сборки** в **Team Explorer** в Visual Studio.  Убедившись, что сборка запускается успешно, определите конвейер выпуска, который развертывает приложение в кластер.

Убедитесь, что развертывание выполнено успешно и приложение выполняется в кластере.  Откройте браузер и перейдите по ссылке [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Обратите внимание на версию приложения, в этом случае — 1.0.0.20170815.3.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Обновление приложения

Измените код в приложении.  Сохраните и зафиксируйте изменения, выполнив указанные выше действия.

После начала обновления вы сможете отслеживать ход обновления в Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

Обновление приложения может занять несколько минут. После завершения обновления будет запущена новая версия приложения.  В этом примере — 1.0.0.20170815.4.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Добавление проекта в систему управления версиями
> * Создание конвейера сборки
> * Создание конвейера выпуска
> * Автоматическое развертывание и обновление приложения

Перейдите к следующему руководству:
> [Настройка мониторинга и диагностики приложения](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
