---
title: Требование безопасной передачи в службе хранилища Azure | Документация Майкрософт
description: Узнайте о функции "Требуется безопасное перемещение" для службы хранилища Azure и о том, как ее включить.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/20/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: e75cd873b780f514b24ee254dd491b6aa779c420
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101930"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Требование безопасной передачи в службе хранилища Azure

Параметр "Требуется безопасное перемещение" повышает безопасность учетной записи хранения, разрешая выполнять к ней запросы, отправленные только посредством безопасных подключений. Например, при вызове интерфейсов REST API для доступа к своей учетной записи хранения необходимо подключиться с помощью протокола HTTPS. Функция "Требуется безопасное перемещение" отклоняет запросы, использующие протокол HTTP.

Кроме того, если этот параметр включен, то при использовании службы файлов Azure любое подключение без шифрования завершается сбоем. Это относится к сценариям, включающим в себя SMB 2.1, SMB 3.0 без шифрования и некоторые версии SMB-клиента Linux. 

Если вы создаете учетную запись хранения с помощью пакета SDK, параметр "Требуется безопасное перемещение" отключен по умолчанию. Этот параметр включен по умолчанию, если вы создаете учетную запись хранения на портале Azure.

> [!NOTE]
> Так как служба хранилища Azure не поддерживает протокол HTTPS для имен личных доменов, при использовании данных имен этот параметр не применяется. Классические учетные записи хранения не поддерживаются.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Включение параметра "Требуется безопасное перемещение" на портале Azure

Параметр "Требуется безопасное перемещение" можно включить при создании учетной записи хранения на [портале Azure](https://portal.azure.com). Его также можно включить для имеющихся учетных записей хранения.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Включение требования безопасной передачи для новой учетной записи хранения

1. Откройте область **Создание учетной записи хранения** на портале Azure.
1. В разделе **Требуется безопасное перемещение** щелкните **Включено**.

   ![Колонка "Создание учетной записи хранения"](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Включение требования безопасной передачи для существующей учетной записи хранения

1. Выберите учетную запись хранения на портале Azure.
1. В области меню учетной записи хранения в разделе **Параметры** щелкните **Конфигурация**.
1. В разделе **Требуется безопасное перемещение** щелкните **Включено**.

   ![Область меню учетной записи хранения](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Включение параметра "Требуется безопасное перемещение" программным способом

Чтобы программно включить требование безопасного перемещения, укажите параметр _supportsHttpsTrafficOnly_ в свойствах учетной записи хранения с помощью REST API, инструментов или библиотек:

* [REST API](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (версия 2016-12-01);
* [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) (версия 0.7)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (версия 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (версия 1.1.0);
* [пакет SDK для .NET ](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (версия 6.3.0);
* [пакет SDK для Python](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (версия 1.1.0);
* [пакет SDK для Ruby](https://rubygems.org/gems/azure_mgmt_storage) (версия 0.11.0).

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Включение параметра Secure transfer required (Требуется безопасное перемещение) с помощью PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Для работы с этим примером требуется модуль Azure PowerShell Az 0.7 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

Выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure.

 Для проверки параметра можно использовать командную строку ниже:

```powershell
> Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Для включения параметра можно использовать командную строку ниже:

```powershell
> Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Включение параметра Secure transfer required (Требуется безопасное перемещение) с помощью интерфейса командной строки

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Для проверки параметра можно использовать командную строку ниже:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Для включения параметра можно использовать командную строку ниже:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Дальнейшие действия
Служба хранилища Azure предоставляет полный набор возможностей обеспечения безопасности, которые в совокупности позволяют разработчикам создавать защищенные приложения. Дополнительные сведения можно получить в [руководстве по безопасности службы хранилища](storage-security-guide.md).
