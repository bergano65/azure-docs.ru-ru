---
title: Обновление свойств конфигурации хранилища служб восстановления с помощью REST API
description: Из этой статьи вы узнаете, как обновить конфигурацию хранилища с помощью REST API.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: cbd958dd71d2d62f7b4c7e8d66ab7e56dc679a51
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74998749"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Обновление конфигураций хранилища служб восстановления Azure с помощью REST API

В этой статье описывается, как обновить конфигурации, связанные с резервным копированием, в хранилище служб восстановления Azure с помощью REST API.

## <a name="soft-delete-state"></a>Состояние обратимого удаления

Удаление резервных копий защищенного элемента является важной операцией, которую необходимо отслеживать. Чтобы защититься от случайного удаления, в хранилище служб восстановления Azure есть возможность обратимого удаления. Эта возможность позволяет клиентам восстанавливать удаленные резервные копии, если это необходимо, за определенный период времени после удаления.

Но существуют сценарии, в которых эта возможность не требуется. Хранилище служб восстановления Azure не может быть удалено, если в нем есть элементы резервного копирования, даже обратимо удаленные. Это может привести к проблеме, если необходимо немедленно удалить хранилище. Например: операции развертывания часто удаляют созданные ресурсы в том же рабочем процессе. Развертывание может создать хранилище, настроить резервное копирование для элемента, выполнить тестовое восстановление и перейти к удалению архивных элементов и хранилища. В случае сбоя удаления хранилища может произойти сбой всего развертывания. Отключение обратимого удаления является единственным способом гарантировать немедленное удаление.

Таким образом, клиенту необходимо тщательно выбрать, следует ли отключить обратимое удаление для определенного хранилища в зависимости от сценария. Дополнительные сведения см. в [статье об обратимом удалении](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Получение состояния обратимого удаления с помощью REST API

По умолчанию состояние обратимого удаления будет включено для всех вновь создаваемых хранилищ служб восстановления. Чтобы получить или обновить состояние обратимого удаления для хранилища, используйте [REST API документ](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) , связанный с конфигурацией резервного хранилища.

Чтобы получить текущее состояние обратимого удаления для хранилища, используйте следующую операцию *Get* .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

URI GET содержит `{subscriptionId}`, `{vaultName}``{vaultresourceGroupName}` параметров. В этом примере `{vaultName}` имеет значение "Testvault задано", а `{vaultresourceGroupName}` — "Тестваултрг". Так как все необходимые параметры указаны в URI, текст запроса не требуется.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Ответы

Успешный ответ для операции GET показан ниже:

|Name  |Тип  |Описание  |
|---------|---------|---------|
|200 ОК     |   [баккупресаурцеваултконфиг](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | ОК        |

##### <a name="example-response"></a>Пример ответа

После отправки запроса GET возвращается ответ 200 (успешно).

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

### <a name="update-soft-delete-state-using-rest-api"></a>Обновление состояния обратимого удаления с помощью REST API

Чтобы обновить состояние обратимого удаления хранилища служб восстановления с помощью REST API, используйте следующую операцию *исправления* .

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

URI исправления содержит `{subscriptionId}`, `{vaultName}``{vaultresourceGroupName}` параметров. В этом примере `{vaultName}` имеет значение "Testvault задано", а `{vaultresourceGroupName}` — "Тестваултрг". Если заменить URI приведенными выше значениями, URI будет выглядеть следующим образом.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Создание текста запроса

Для создания текста запроса используются следующие общие определения.

Дополнительные сведения см. [в документации по REST API](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Name  |Обязательно для заполнения  |Тип  |Описание  |
|---------|---------|---------|---------|
|eTag     |         |   Строка      |  Необязательный eTag       |
|location     |  true       |Строка         |   Расположение ресурса      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Свойства хранилища       |
|tags     |         | Объекты        |     Теги ресурсов    |

#### <a name="example-request-body"></a>Примеры текста запроса

Следующий пример используется для обновления нерабочем с обратимым удалением на "Disabled".

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Ответы

Успешный ответ для операции PATCH показан ниже:

|Name  |Тип  |Описание  |
|---------|---------|---------|
|200 ОК     |   [баккупресаурцеваултконфиг](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | ОК        |

##### <a name="example-response"></a>Пример ответа

После отправки запроса PATCH возвращается ответ 200 (успех).

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