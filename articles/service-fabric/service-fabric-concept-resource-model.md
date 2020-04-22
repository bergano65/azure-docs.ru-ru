---
title: Модель прикладного приложения Azure Service Fabric
description: В этой статье содержится обзор управления приложением Azure Service Fabric с помощью менеджера ресурсов Azure.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682645"
---
# <a name="service-fabric-application-resource-model"></a>Модель прикладного ресурса Service Fabric

У вас есть несколько вариантов развертывания приложений Azure Service Fabric в кластере Service Fabric. Рекомендуется использовать менеджер ресурсов Azure. Если вы используете ресурсный менеджер, можно описать приложения и службы в JSON, а затем развернуть их в том же шаблоне ресурсного менеджера, что и кластер. В отличие от использования PowerShell или Azure CLI для развертывания и управления приложениями, если вы используете менеджер ресурсов, вам не придется ждать, пока кластер будет готов; регистрация заявлений, подготовка и развертывание могут произойти в один шаг. Использование ресурсного менеджера — это лучший способ управлять жизненным циклом приложения в кластере. Для получения дополнительной информации [см.](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)

Управление приложениями в качестве ресурсов в ресурсах менеджера ресурсов может помочь вам добиться улучшений в следующих областях:

* Аудит след: Менеджер ресурсов проверяет каждую операцию и ведет подробный журнал действий. Журнал действий может помочь отследить все изменения, внесенные в приложения и кластер.
* Управление доступом на основе ролей: Вы можете управлять доступом к кластерам и приложениям, развернутым в кластере, используя тот же шаблон resource Manager.
* Эффективность управления: использование диспетчера ресурсов дает единое местоположение (портал Azure) для управления кластерными и критическими развертываниями приложений.

В этом документе вы узнаете, как:

> [!div class="checklist"]
>
> * Развертывание ресурсов приложений с помощью диспетчера ресурсов.
> * Обновление ресурсов приложения с помощью менеджера ресурсов.
> * Удалить ресурсы приложений.

## <a name="deploy-application-resources"></a>Развертывание ресурсов приложений

Высокоуровневые шаги, предпринимаемые для развертывания приложения и его служб с помощью модели ресурсов ресурса Resource Manager:
1. Упакуйкодикодикоди приложения.
1. Загрузите пакет.
1. Ссылка на местоположение пакета в шаблоне менеджера ресурсов в качестве ресурса приложения. 

Для получения дополнительной информации просмотрите [приложение](service-fabric-package-apps.md#create-an-sfpkg).

Затем необходимо создать шаблон resource Manager, обновить файл параметров с деталями приложения и развернуть шаблон в кластере Service Fabric. [Исследуйте образцы](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Создание учетной записи хранения

Чтобы развернуть приложение из шаблона «Менеджер ресурсов», необходимо иметь учетную запись хранения. Учетная запись хранилища используется для постановки изображения приложения. 

Можно повторно использовать существующую учетную запись хранения или создать новую учетную запись для хранения приложений. Если вы используете существующую учетную запись хранения, вы можете пропустить этот шаг. 

![Создание учетной записи хранения][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Настройка учетной записи хранилища

После создания учетной записи хранилища создается контейнер с каплей, в котором приложения могут быть поставлены. На портале Azure перейдите на учетную запись хранения Azure, где требуется хранить приложения. Выберите **Blobs** > **Добавить контейнер**. 

Ресурсы в кластере могут быть защищены, установив уровень общедоступного доступа на **частный.** Вы можете предоставить доступ несколькими способами:

* Разрешить доступ к каплям и очередям с помощью [Active Directory Azure.](../storage/common/storage-auth-aad-app.md)
* Предоставите доступ к данным Azure blob и очереди с помощью [RBAC на портале Azure.](../storage/common/storage-auth-aad-rbac-portal.md)
* Делегируйте доступ с помощью [общей подписи доступа.](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)

Пример в следующем скриншоте использует анонимный доступ к чтению для капли.

![Создать BLOB-объект][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Этап приложения в учетной записи хранилища

Прежде чем развернуть приложение, необходимо разместить приложение в хранилище капли. В этом учебнике мы создаем пакет приложений вручную. Имейте в виду, что этот шаг может быть автоматизирован. Для получения дополнительной информации [см.](service-fabric-package-apps.md#create-an-sfpkg) 

В этом учебнике мы используем [образец приложения «Голосование».](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)

1. В Visual Studio, право нажмите **на** голосование проекта, а затем выберите **пакет**.

   ![Пакетное приложение][PackageApplication]  
1. Перейдите в каталог *.'service-fabric-dotnet-quickstart-Voting-pkg-Debug.* Застегните содержимое в файл под названием *Voting.zip*. Файл *ApplicationManifest.xml* должен быть в корне в файле застежки-молнии.

   ![Приложение «Зип»][ZipApplication]  
1. Переименуйте файл, чтобы изменить расширение с .zip на *.sfpkg.*

1. На портале Azure в контейнере **приложений** для учетной записи хранилища выберите **Upload,** а затем загрузите **Voting.sfpkg.** 

   ![Загрузить пакет приложений][UploadAppPkg]

Теперь приложение поэтапно, и для развертывания приложения можно создать шаблон менеджера ресурсов.

### <a name="create-the-resource-manager-template"></a>Создание шаблона Resource Manager

В примере приложения содержатся [шаблоны Azure Resource Manager,](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) которые можно использовать для развертывания приложения. Имена файлов *шаблонов: UserApp.json* и *UserApp.Parameters.json*.

> [!NOTE]
> Файл *UserApp.Parameters.json* должен быть обновлен с именем кластера.
>
>

| Параметр              | Описание                                 | Пример                                                      | Комментарии                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Название кластера, который вы развертываете | sf-кластер123                                                |                                                              |
| application            | Имя приложения                 | Голосования                                                       |
| applicationTypeName    | Имя типа приложения           | ГолосованиеТип                                                   | Должен соответствовать ApplicationManifest.xml                 |
| приложениеTypeVersion | Версия типа приложения         | 1.0.0                                                        | Должен соответствовать ApplicationManifest.xml                 |
| serviceName            | Название службы         | Голосование                                             | Должно быть в формате ApplicationName-ServiceType            |
| serviceTypeName        | Имя типа службы                | VotingWeb                                                    | Должен соответствовать ServiceManifest.xml                 |
| appPackageUrl          | URL-адрес хранения капли приложения     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | URL-адрес пакета приложений в хранилище blob (процедура установки URL описана позже в статье) |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Развертывание приложения

Выполнить смдlet **New-AzResourceGroupDeployment** для развертывания приложения в группе ресурсов, содержащей кластер:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Обновление приложения Service Fabric с помощью менеджера ресурсов

Можно обновить приложение, уже развернутое в кластере Service Fabric по одной из следующих причин:

* В приложение добавляется новая услуга. Определение службы должно быть добавлено в файлы *service-manifest.xml* и *application-manifest.xml* при добавлении службы в приложение. Чтобы отразить новую версию приложения, необходимо также изменить версию типа приложения с 1.0.0 до 1.0.1 в [UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* В приложение добавляется новая версия существующей службы. Примеры включают изменения кода приложения и обновления для версии и имени типа приложения. Для этого обновления, обновление UserApp.Parameters.json, как это:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Удаление ресурсов приложения

Чтобы удалить приложение, развернутое с помощью модели ресурсов приложения в ресурсном менеджере:

1. Используйте cmdlet [Get-AzResource,](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) чтобы получить идентификатор ресурса для приложения:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Для удаления ресурсов приложения используйте cmdlet [Remove-AzResource:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Следующие шаги

Получите информацию о модели ресурса приложения:

* [Моделирование приложения в Service Fabric](service-fabric-application-model.md)
* [Манифесты службы и приложения Service Fabric](service-fabric-application-and-service-manifests.md)
* [Лучшие практики: Инфраструктура как код](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Управление приложениями и службами как ресурсами Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
