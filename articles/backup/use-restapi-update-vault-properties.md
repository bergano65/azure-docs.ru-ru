---
title: Конфигурация хранилища служб восстановления обновления с помощью REST API
description: В этой статье узнайте, как обновить конфигурацию хранилища с помощью REST API.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252367"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Обновление конфигураций Убежища восстановления Azure с помощью REST API

В этой статье описывается, как обновлять конфигурации, связанные с резервным копированием, в хранилище служб восстановления Azure с помощью REST API.

## <a name="soft-delete-state"></a>Мягкое состояние удаления

Удалять резервные отпор защищенного элемента является значительной операцией, которая должна контролироваться. Для защиты от случайных удалений хранилище служб восстановления Azure имеет возможность мягкого удаления. Эта возможность позволяет клиентам восстановить удаленные резервные копирования, при необходимости, в течение периода времени после удаления.

Но есть сценарии, в которых эта возможность не требуется. Хранилище служб восстановления Azure не может быть удалено, если в нем есть элементы резервного копирования, даже удаленные. Это может создать проблему, если хранилище должно быть немедленно удалено. Например: операции развертывания часто очищают созданные ресурсы в одном и том же рабочем процессе. Развертывание может создать хранилище, настроить резервные ожидания для элемента, сделать тест восстановления, а затем приступить к удалению элементов резервного копирования и хранилище. Если удаление хранилища завершается неудачей, все развертывание может выполниться. Отключение мягкого удаления является единственным способом гарантировать немедленное удаление.

Таким образом, клиент должен тщательно выбирать, следует ли отключить мягкое удаление для конкретного хранилища в зависимости от сценария. Для получения дополнительной информации, [см.](backup-azure-security-feature-cloud.md#soft-delete)

### <a name="fetch-soft-delete-state-using-rest-api"></a>Получить мягкое состояние удаления с помощью REST API

По умолчанию состояние "мягкого удаления" будет включено для любого недавно созданного хранилища служб восстановления. Чтобы получить/обновить состояние мягкого удаления для хранилища, используйте документ, связанный с конфигурацией хранилища резервного копирования [REST API](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs)

Чтобы получить текущее состояние мягкого удаления для хранилища, используйте следующую операцию *GET*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

GET URI `{subscriptionId}`имеет `{vaultName}` `{vaultresourceGroupName}` , , параметры. В этом `{vaultName}` примере , является `{vaultresourceGroupName}` "testVault" и является "testVaultRG". Так как все необходимые параметры указаны в URI, текст запроса не требуется.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Ответы

Успешный ответ на операцию 'GET' показан ниже:

|name  |Тип  |Описание  |
|---------|---------|---------|
|200 ОК     |   [Резервное копированиеРесурсVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Пример ответа

После отправки запроса 'GET' возвращается 200 (успешный) ответ.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Обновление мягкого состояния удаления с помощью REST API

Для обновления состояния хранилища служб восстановления с помощью REST API используйте следующую операцию *PATCH*

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

PATCH URI `{subscriptionId}`имеет `{vaultName}` `{vaultresourceGroupName}` , , параметры. В этом `{vaultName}` примере , является `{vaultresourceGroupName}` "testVault" и является "testVaultRG". Если мы заменим URI с значениями выше, то URI будет выглядеть следующим образом.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Создание текста запроса

Он следующие общие определения используются для создания органа запроса

Для получения более подробной информации обратитесь к [документации REST API](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|name  |Обязательно  |Тип  |Описание  |
|---------|---------|---------|---------|
|eTag     |         |   Строка      |  Необязательный eTag       |
|location     |  Да       |Строка         |   Расположение ресурса      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Свойства хранилища       |
|tags     |         | Объект        |     Теги ресурсов    |

#### <a name="example-request-body"></a>Примеры текста запроса

Следующий пример используется для обновления состояния мягкого удаления до "отключенного".

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Ответы

Успешный ответ на операцию «PATCH» показан ниже:

|name  |Тип  |Описание  |
|---------|---------|---------|
|200 ОК     |   [Резервное копированиеРесурсVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Пример ответа

После отправки запроса 'PATCH' возвращается 200 (успешный) ответ.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

[Create Azure Recovery Services backup policies using REST API](backup-azure-arm-userestapi-createorupdatepolicy.md) (Создание политик резервного копирования Служб восстановления Azure с помощью REST API)

Дополнительные сведения о REST API Azure см. в следующих документах:

- [Recovery Services](/rest/api/recoveryservices/) (Службы восстановления)
- [Начало работы с Azure REST API](/rest/api/azure/)
