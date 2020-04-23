---
title: Требуется безопасная передача для обеспечения безопасных подключений
titleSuffix: Azure Storage
description: Узнайте, как потребовать безопасной передачи запросов в Хранилище Azure. Когда требуется безопасная передача для учетной записи хранилища, любые запросы, поступающие из небезопасного соединения, отклоняются.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 125f4188ed3f12f366c619af9efe3aa203987c19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870519"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Требуется безопасная передача для обеспечения безопасных подключений

Вы можете настроить учетную запись хранилища для приема запросов от защищенных подключений только путем установки необходимого свойства **Безопасной передачи** для учетной записи хранилища. При требуется безопасная передача, любые запросы, поступающие из небезопасного соединения, отклоняются. Корпорация Майкрософт рекомендует всегда требовать безопасного перевода для всех учетных записей хранения данных.

При необходимости безопасной передачи на операцию API-извне Хранилища Azure необходимо сделать через HTTPS. Любой запрос, сделанный по HTTP, отклоняется.

Подключение к совместной акции Azure File через SMB без шифрования не удается, когда для учетной записи хранилища требуется безопасная передача. Примеры небезопасных соединений включают в себя те, сделанные более SMB 2.1, SMB 3.0 без шифрования, или некоторые версии клиента Linux SMB.

По умолчанию при создании учетной записи хранилища включено необходимое свойство **безопасной передачи.**

> [!NOTE]
> Так как служба хранилища Azure не поддерживает протокол HTTPS для имен личных доменов, при использовании данных имен этот параметр не применяется. Классические учетные записи хранения не поддерживаются.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Требуется безопасная передача на портале Azure

При создании учетной записи хранилища на [портале Azure](https://portal.azure.com)можно включить требуемое свойство **secure transfer.** Его также можно включить для имеющихся учетных записей хранения.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Включение требования безопасной передачи для новой учетной записи хранения

1. Откройте область **Создание учетной записи хранения** на портале Azure.
1. В разделе **Требуется безопасное перемещение** щелкните **Включено**.

   ![Колонка "Создание учетной записи хранения"](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Включение требования безопасной передачи для существующей учетной записи хранения

1. Выберите учетную запись хранения на портале Azure.
1. В области меню учетной записи хранения в разделе **Параметры** щелкните **Конфигурация**.
1. В разделе **Требуется безопасное перемещение** щелкните **Включено**.

   ![Область меню учетной записи хранения](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Требуется безопасная передача из кода

Чтобы требовать безопасной передачи программно, установите _свойство supportsHttpsTrafficOnly_ на учетную запись хранения. Вы можете установить это свойство с помощью aPI-ипой REST-поставщика ресурсов хранения данных, клиентских библиотек или инструментов:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)
* [Пакет SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Питон SDK](https://pypi.org/project/azure-mgmt-storage)
* [Пакет SDK для Ruby](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Требуется безопасная передача с PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Для работы с этим примером требуется модуль Azure PowerShell Az 0.7 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

Выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure.

 Для проверки параметра можно использовать командную строку ниже:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Для включения параметра можно использовать командную строку ниже:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Требуется безопасная передача с Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Используйте следующую команду, чтобы проверить настройки:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Используйте следующую команду, чтобы включить настройку:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Дальнейшие действия

[Рекомендации по безопасности для хранения Blob](../blobs/security-recommendations.md)
