---
title: Устранение неполадок развертываний в Resource Manager | Документация Майкрософт
description: Узнайте, как контролировать и устранять неполадки развертываний в Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 89d1573ff03771e5229e1ce28bb4ee7ecec702e3
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332993"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Руководство. Устранение неполадок развертывания шаблонов Resource Manager

Узнайте, как устранять неполадки при развертывании шаблонов Resource Manager. В этом руководстве вы настроите две ошибки в шаблоне и узнаете, как использовать журналы операций и историю развертывания для решения ошибок.

Существует два типа ошибок, связанных с развертыванием шаблона.

- **Ошибки проверки** возникают в сценариях, которые можно определить перед развертыванием. Это синтаксические ошибки в шаблоне или попытки развертывания ресурсов, которые приведут к превышению квот для подписки. 
- **Ошибки развертывания** возникают из-за условий, возникающих во время развертывания. Это попытки получить доступ к ресурсу, который развертывается параллельно.

Ошибки обоих типов возвращают код ошибки, с помощью которого можно устранить неполадки развертывания. Ошибки обоих типов отображаются в журнале действий. Однако ошибки проверки не отображаются в журнале развертывания, так как при их наличии развертывание не запускается.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Создание проблемного шаблона
> * Устранение ошибок проверки
> * Устранение ошибок развертывания
> * Очистка ресурсов

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

- [Visual Studio Code](https://code.visualstudio.com/) с [расширением Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="create-a-problematic-template"></a>Создание проблемного шаблона

Откройте шаблон [Создайте стандартную учетную запись хранения](https://azure.microsoft.com/resources/templates/101-storage-account-create/) из [Шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/) и настройте две ошибки шаблона.

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Измените строку **apiVersion** на следующую.

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** является недопустимым именем элемента. Это ошибка проверки.
    - Версия API должна быть "2018-07-01".  Это ошибка развертывания.

5. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл в качестве **azuredeploy.json** на локальном компьютере.

## <a name="troubleshoot-the-validation-error"></a>Устранение ошибки проверки

Дополнительные сведения о процедуре развертывания шаблона см. в [этом разделе](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template).

Вы получите сообщение об ошибке следующего содержания.

```
New-AzureRmResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Сообщение об ошибке указывает, что проблема связана с **apiVersion1**.

Чтобы устранить проблему, используйте Visual Studio Code, изменив **apiVersion1** на **apiVersion**, а затем сохраните шаблон.

## <a name="troubleshoot-the-deployment-error"></a>Устранение ошибки развертывания

Дополнительные сведения о процедуре развертывания шаблона см. в [этом разделе](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template).

Вы получите сообщение об ошибке следующего содержания.

```
New-AzureRmResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Ошибку развертывания можно найти на портале Azure с помощью следующей процедуры.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Откройте **Группы ресурсов**, а затем введите имя группы ресурсов. Отобразится **1 Failed** в разделе **Развертывание**.

    ![Устранение неполадок руководства Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Выберите **Сведения об ошибке**.

    ![Устранение неполадок руководства Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Сообщение об ошибке совпадает с сообщением, показанным ранее.

    ![Устранение неполадок руководства Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Также можно найти ошибки в журналах действий.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите **Монитор** > **Журнал действий**.
3. Используйте фильтры для поиска журнала.

    ![Устранение неполадок руководства Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Используйте Visual Studio Code для устранения проблемы, а затем повторно разверните шаблон.

Список распространенных ошибок см. в статье [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](./resource-manager-common-deployment-errors.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как устранять ошибки развертывания шаблона Resource Manager.  Дополнительные сведения см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](./resource-manager-common-deployment-errors.md).