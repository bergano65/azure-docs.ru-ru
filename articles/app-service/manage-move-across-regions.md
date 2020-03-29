---
title: Перемещение приложения в другой регион
description: Узнайте, как перемещать ресурсы Службы приложений из одного региона в другой.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925713"
---
# <a name="move-an-app-service-app-to-another-region"></a>Перемещение приложения Службы приложений в другой регион

В этой статье описывается, как переместить ресурсы службы приложений в другой регион Azure. Вы можете переместить свои ресурсы в другой регион по ряду причин. Например, использовать новый регион Azure, развертывать функции или службы, доступные только в определенных регионах, удовлетворять требованиям внутренней политики и управления или в ответ на требования к планированию емкости.

Ресурсы Службы приложений специфичны для регионов и не могут перемещаться по регионам. Необходимо создать копию существующих ресурсов Службы приложений в целевом регионе, перенести содержимое в новое приложение. Если приложение-источник использует пользовательский домен, вы можете [перенести его в новое приложение в целевом регионе,](manage-custom-dns-migrate-domain.md) когда вы закончите.

Чтобы сделать копирование приложения проще, вы можете [клонировать отдельное приложение Службы приложений](app-service-web-app-cloning.md) в план Службы Приложений в другом регионе, но у него есть [ограничения,](app-service-web-app-cloning.md#current-restrictions)особенно в том, что оно не поддерживает приложения Linux.

## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что приложение App Service находится в регионе Azure, из которого вы хотите переместиться.
- Убедитесь, что целевой регион поддерживает службу поддержки приложений и любую связанную службу, ресурсы которых вы хотите переместить.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Подготовка.

Определите все ресурсы Службы приложений, которые вы используете в настоящее время. Пример:

- Приложения службы приложений
- [Планы службы приложений](overview-hosting-plans.md)
- [Слоты развертывания](deploy-staging-slots.md)
- [Пользовательские домены, приобретенные в Azure](manage-custom-dns-buy-domain.md)
- [Сертификаты SSL](configure-ssl-certificate.md)
- [Интеграция виртуальной сети Azure](web-sites-integrate-with-vnet.md)
- [Гибридные соединения](app-service-hybrid-connections.md).
- [Управляемые идентификаторы](overview-managed-identity.md)
- [Настройки резервного копирования](manage-backup.md)

Некоторые ресурсы, такие как импортированные сертификаты или гибридные соединения, содержат интеграцию с другими службами Azure. Для получения информации о том, как переместить эти ресурсы в разных регионах, см.

## <a name="move"></a>Переместить

1. [Создайте резервную часть исходного приложения.](manage-backup.md)
1. [Создайте приложение в новом плане службы приложений в целевом регионе.](app-service-plan-manage.md#create-an-app-service-plan)
2. [Восстановление резервного копирования в целевом приложении](web-sites-restore.md)
2. Если вы используете пользовательский домен, [привязать его упреждаюстражей к целевому приложению](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) и `awverify.` [включить домен в целевом приложении.](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)
3. Назначьте все остальное в целевом приложении, чтобы быть таким же, как исходное приложение и проверить конфигурацию.
4. Когда вы будете готовы к пользовательскому домену, чтобы указать на целевое приложение, [переобрасьте доменное имя.](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Очистка исходных ресурсов

Удалите исходное приложение и план службы приложений. [План службы приложений в несвободном уровне несет плату, даже если в нем нет приложения.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Дальнейшие действия

[Клонирование приложений службы приложений Azure с помощью PowerShell](app-service-web-app-cloning.md)