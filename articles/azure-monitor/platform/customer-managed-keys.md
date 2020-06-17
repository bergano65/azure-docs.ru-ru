---
title: Ключ Azure Monitor, управляемый клиентом
description: Сведения и действия по настройке ключа, управляемого клиентом (CMK), для шифрования данных в рабочих областях Log Analytics с помощью ключа Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/20/2020
ms.openlocfilehash: 037edb8af6e04a2ff65977a92a66482c9f4f880f
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845104"
---
# <a name="azure-monitor-customer-managed-key"></a>Ключ Azure Monitor, управляемый клиентом 

В этой статье содержатся общие сведения и инструкции по настройке управляемых клиентом ключей (CMK) для рабочих областей Log Analytics. После их настройки все данные, отправленные в рабочие области, шифруются с помощью ключа Azure Key Vault.

Перед настройкой рекомендуется ознакомиться с разделом [Пределы и ограничения](#limitationsandconstraints) ниже.

## <a name="customer-managed-key-cmk-overview"></a>Общие сведения о ключе, управляемом клиентом (CMK)

[Шифрование неактивных данных](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) является стандартным требованием к конфиденциальности и безопасности в организациях. Вы можете позволить Azure полностью управлять шифрованием неактивных данных, при этом имея различные варианты для непосредственного управления шифрованием или ключами шифрования.

Azure Monitor гарантирует, что все неактивные данные будут шифроваться с помощью ключей, управляемых Azure. Azure Monitor также предоставляет возможность шифрования данных с помощью собственного ключа, хранящегося в  [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) , доступ к которому осуществляется через хранилище с использованием назначенной системой проверки подлинности на основе  [управляемого удостоверения](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) . Этот ключ может быть  [защищенным либо с помощью программного обеспечения, либо с помощью оборудования HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Использование шифрования в Azure Monitor идентично использованию  [шифрования в службе хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

CMK позволяет управлять доступом к данным и отзывать его в любое время. Хранилище Azure Monitor всегда учитывает изменения в разрешениях на доступ к ключам в пределах часа. Данные, полученные за последние 14 дней, также хранятся в кэше горячего уровня доступа (с поддержкой SSD) для эффективной работы обработчика запросов. Эти данные остаются зашифрованными с помощью ключей Майкрософт, независимо от конфигурации CMK, но ваш контроль над данными SSD должен соответствовать  [отзыву ключей](#cmk-kek-revocation). Мы работаем над тем, чтобы данные SSD были зашифрованы с помощью CMK во второй половине 2020 года.

Возможность CMK поставляется в выделенных кластерах Log Analytics. Чтобы убедиться в наличии необходимой емкости в вашем регионе, нам нужно, чтобы ваша подписка была заранее внесена в список разрешений. Перед началом настройки CMK используйте службу технической поддержки Майкрософт, чтобы получить список разрешений подписки.

 [Кластерная модель ценообразования Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters) использует резервирования мощности, начиная с уровня 1000 ГБ/день.

## <a name="how-cmk-works-in-azure-monitor"></a>Как работает CMK в Azure Monitor

Azure Monitor использует назначаемое системой управляемое удостоверение для предоставления доступа к Azure Key Vault. Управляемое системой удостоверение можно связать только с одним ресурсом Azure, в то время как удостоверение кластера Log Analytics поддерживается на уровне кластера. Это указывает на то, что возможность CMK поставляется в выделенный кластер Log Analytics. Для поддержки CMK в нескольких рабочих областях новый ресурс Log Analytics *кластера* выполняется в качестве промежуточного подключения между Key Vault и рабочими областями Log Analytics. Хранилище кластеров Log Analytics использует управляемое удостоверение, связанное \'с ресурсом *Кластер*, для проверки подлинности Azure Key Vault через Azure Active Directory. 

После настройки CMK все данные, принимаемые в рабочие области, связанные с ресурсом *Кластер*, шифруются с помощью ключа в Key Vault. Вы можете отсоединить рабочие области от ресурса *Кластер* в любое время. Новые данные попадают в хранилище Log Analytics и шифруются с помощью ключа Майкрософт, в то время как вы можете легко запрашивать новые и старые данные.


![Общие сведения о CMK](media/customer-managed-keys/cmk-overview-8bit.png)

1. Key Vault
2. Ресурс *Кластер* Log Analytics, имеющий управляемое удостоверение с разрешениями на Key Vault. Удостоверение распространяется на подчиненное выделенное хранилище кластера Log Analytics.
3. Выделенный кластер Log Analytics.
4. Рабочие области, связанные с ресурсом *Кластер* для шифрования CMK.

## <a name="encryption-keys-operation"></a>Операция с ключами шифрования

Существует 3 типа ключей, участвующих в шифровании данных службы хранилища:

- **KEK** — ключ шифрования ключей (CMK)
- **AEK** — ключ шифрования учетной записи
- **DEK** — ключ шифрования данных

Применяются следующие правила.

- Учетные записи хранения кластера Log Analytics создают уникальный ключ шифрования для каждой учетной записи хранения, который называется АЕК.

- АЕК используется для получения DEK, которые являются ключами, используемыми для шифрования каждого блока данных, записываемых на диск.

- Когда вы настраиваете ключ в Key Vault и ссылаетесь на него в ресурсе *Кластер*, служба хранилища Azure отправляет запросы в Azure Key Vault, чтобы перенести и распаковать АЕК для выполнения операций шифрования и расшифровки данных.

- Ваш KEK никогда не покидает Key Vault, а если он является ключом HSM — не покидает аппаратное обеспечение.

- Служба хранилища Azure использует управляемое удостоверение, связанное с ресурсом *Кластер*, для проверки подлинности и получения доступа к Azure Key Vault через Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Процедура подготовки CMK

1. Список разрешений подписки. Возможность CMK поставляется в выделенных кластерах Log Analytics. Чтобы убедиться в наличии необходимой емкости в вашем регионе, нам нужно, чтобы ваша подписка была заранее внесена в список разрешений. Обратитесь к контактному лицу корпорации Майкрософт, чтобы внести подписку в список разрешений.
2. Создание Azure Key Vault и ключа хранилища.
3. Создание ресурса *Кластер*.
4. Предоставление разрешений для вашего Key Vault.
5. Связывание рабочих областей Log Analytics.

В настоящее время процедура не поддерживается в пользовательском интерфейсе, а процесс подготовки выполняется с помощью REST API.

> [!IMPORTANT]
> Любой запрос API должен содержать маркер авторизации носителя в заголовке запроса.

Пример:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

где *eyJ0eXAiO....* представляет полный маркер авторизации. 

Получить маркер можно с помощью одного из следующих методов:

1. Используйте метод [Регистрация приложений](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens).
2. На портале Azure
    1. Перейдите на портал Azure, используя "инструмент разработчика" (F12).
    1. Найдите строку авторизации в разделе "Заголовки запросов" в одном из экземпляров "batch?api-version". Он выглядит следующим образом: "authorization: Bearer eyJ0eXAiO....". 
    1. Скопируйте и добавьте его в вызов API, как показано в приведенных ниже примерах.
3. Перейдите на сайт документации по Azure REST. Нажмите кнопку "Попробовать" для любого API и скопируйте маркер носителя.

### <a name="asynchronous-operations-and-status-check"></a>Асинхронные операции и проверка состояния

Некоторые операции в этой процедуре конфигурации выполняются асинхронно, так как они не могут быть завершены быстро. Ответ для асинхронной операции изначально возвращает код состояния HTTP 200 (ОК) и заголовок со свойством *Azure-AsyncOperation*, если оно принято:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Вы можете проверить состояние асинхронной операции, отправив запрос GET значению заголовка *Azure-AsyncOperation*:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

Ответ содержит сведения об операции, а также ее *состояние*. Он может принимать одно из следующих значений:

Операция выполняется
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

Выполняется операция обновления идентификатора ключа.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Выполняется удаление ресурса *Кластер*. При удалении ресурса *Кластер*, в котором есть рабочие области, связанные с рабочими областями, операция разъединения выполняется для каждой из рабочих областей в асинхронных операциях, которые могут занять некоторое время.
Это не имеет значения при удалении *Кластера* без связанной рабочей области. В этом случае ресурс *Кластер* удаляется немедленно.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Операция завершена
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Ошибка при выполнении операции
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="subscription-whitelisting"></a>Список разрешений подписки

Возможность CMK поставляется в выделенных кластерах Log Analytics. Чтобы убедиться в наличии необходимой емкости в вашем регионе, нам нужно, чтобы ваша подписка была заранее внесена в список разрешений. Используйте свои контактные данные в Майкрософт, чтобы предоставить идентификаторы подписок.

> [!IMPORTANT]
> Функция CMK является региональной. Azure Key Vault, ресурс *Кластер* и связанные рабочие области Log Analytics должны находиться в одном регионе, однако могут располагаться в разных подписках.

### <a name="storing-encryption-key-kek"></a>Хранение ключа шифрования (KEK)

Создайте или используйте решение Azure Key Vault, которое вы должны были уже создать, или импортируйте ключ, который будет использоваться для шифрования данных. Для Azure Key Vault нужно настроить возможность восстановления, чтобы обезопасить ключи и доступ к данным в Azure Monitor. Вы можете проверить эту конфигурацию в свойствах вашего Key Vault. Следует включить пункты *Обратимое удаление* и *Защита от очистки*.

![Параметры обратимого удаления и защиты от очистки](media/customer-managed-keys/soft-purge-protection.png)

Эти параметры доступны через CLI и PowerShell.
- [обратимое удаление](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Защита от очистки](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) защищает секрет и данные хранилища от принудительного удаления даже после обратимого удаления.

### <a name="create-cluster-resource"></a>Создание ресурса *Кластер*

Этот ресурс используется в качестве промежуточного подключения идентификатора между Key Vault и рабочими областями Log Analytics. После получения подтверждения о том, что ваши подписки были внесены в список разрешений, создайте ресурс *Кластер* Log Analytics в регионе, в котором находятся ваши рабочие области.

При создании ресурса *Кластер* необходимо указать уровень *резервирования мощности* (SKU). *Уровень резервирования мощности* может находиться в диапазоне от 1 000 до 2 000 ГБ в день, и его можно обновлять шагами по 100 единиц позже. Если требуется уровень резервирования мощности более 2 000 ГБ в день, свяжитесь с нами по адресу LAIngestionRate@microsoft.com. [Дополнительные сведения](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

Свойство *billingType* определяет атрибуты выставления счетов для ресурса *Кластер* и его данных:
- *кластер* (по умолчанию) — выставление счетов относится к подписке, в которой размещен ваш ресурс *Кластер*;
- *рабочие области* — выставление счетов относится к подпискам, в которых пропорционально размещаются ваши рабочие области.

> [!NOTE]
> После создания ресурса *Кластер* его можно обновить с помощью *sku*, *keyVaultProperties* или *billingType* с помощью запроса PATCH REST.

**Создание**

Этот запрос Resource Manager является асинхронной операцией.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

Удостоверение назначается ресурсу *Кластер* во время создания.

**Ответ**

200 ОК и заголовок.

Хотя подготовка кластера Log Analytics занимает некоторое время, вы можете проверить состояние подготовки двумя способами:

1. Скопируйте значение URL-адреса Azure-AsyncOperation из ответа и выполните [проверку состояния асинхронных операций](#asynchronous-operations-and-status-check).
2. Отправьте запрос GET на ресурс *Кластер* и просмотрите значение *provisioningState*. *ProvisioningAccount* — во время подготовки и *Succeeded* — после завершения.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Скопируйте и сохраните ответ, так как эти сведения понадобятся вам в дальнейших действиях.

**Ответ**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "provisioningState": "ProvisioningAccount",
    "clusterType": "LogAnalytics",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

Идентификатор GUID "principalId" создается службой управляемого удостоверения для ресурса *Кластер*.

### <a name="grant-key-vault-permissions"></a>Предоставление разрешений Key Vault

Обновите Key Vault с помощью новой политики доступа, которая предоставляет разрешения для ресурса *Кластер*. Эти разрешения используются в базовой службе хранилища Azure Monitor для шифрования данных. Откройте Key Vault на портале Azure и щелкните "Политики доступа", а затем "+ Добавить политику доступа", чтобы создать политику с такими параметрами:

- Разрешения ключа: выберите "Получение", "Упаковка ключа" и "Распаковка ключа".
- Выбор субъекта: введите значение идентификатора субъекта, возвращенное в ответе на предыдущем шаге.

![предоставление разрешений Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Разрешение *Получение* необходимо, чтобы убедиться, что Key Vault настроено как восстанавливаемое для защиты ключа и доступа к данным Azure Monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Обновление ресурса "Кластер" с подробными сведениями об идентификаторе ключа

Этот шаг выполняется во время начального и будущих обновлений версии ключа в Key Vault. Он информирует службу хранилища Azure Monitor о версии ключа, используемой для шифрования данных. При обновлении новый ключ используется для упаковки и распаковки ключа службы хранилища (АЕК).

Чтобы обновить ресурс *Кластер* с помощью сведений *идентификатора ключа* Key Vault, выберите текущую версию ключа в Azure Key Vault и получите сведения об идентификаторе ключа.

![Предоставление разрешений Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Обновите ресурс *Кластер* KeyVaultProperties с подробными сведениями об идентификаторе ключа.

**Update**

Этот запрос Resource Manager — асинхронная операция при обновлении сведений об идентификаторе ключа, хотя она является синхронной при обновлении значения емкости.

> [!NOTE]
> Вы можете указать частичный текст в ресурсе *Кластер* для обновления *sku*, *keyVaultProperties* или *billingType*.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" содержит сведения об идентификаторе ключа Key Vault.

**Ответ**

200 ОК и заголовок.
Завершение распространения идентификатора ключа занимает несколько минут. Проверить состояние обновления можно двумя способами.
1. Скопируйте значение URL-адреса Azure-AsyncOperation из ответа и выполните [проверку состояния асинхронных операций](#asynchronous-operations-and-status-check).
2. Отправьте запрос GET на ресурс *Кластер* и просмотрите свойства *KeyVaultProperties*. В ответе должны возвращаться недавно обновленные сведения об идентификаторе ключа.

Ответ на запрос GET в ресурсе *Кластер* после завершения обновления идентификатора ключа должен выглядеть следующим образом:

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Сопоставление рабочей области с ресурсом *Кластер*

Для выполнения этой операции необходимо иметь разрешения на "запись" как для рабочей области, так и для ресурса *Кластер*, в том числе и на следующие действия:

- В рабочей области: Microsoft.OperationalInsights/workspaces/write
- В ресурсе *Кластер*: Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Этот шаг следует выполнить только после завершения подготовки кластера Log Analytics. Если вы связываете рабочие области и принимаете данные до подготовки, полученные данные будут удалены и их будет невозможно восстановить.

**Связывание рабочей области**

Этот запрос Resource Manager является асинхронной операцией.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Ответ**

200 ОК и заголовок.

После операции сопоставления, которая может занять до 90 минут, полученные данные хранятся в зашифрованном виде с помощью управляемого ключа. Проверить состояние сопоставления рабочей области можно двумя способами.

1. Скопируйте значение URL-адреса Azure-AsyncOperation из ответа и выполните [проверку состояния асинхронных операций](#asynchronous-operations-and-status-check).
2. Отправьте запрос [Рабочие области — получение](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) и оцените ответ. Связанная рабочая область будет иметь идентификатор clusterResourceId в пункте "компоненты".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Ответ**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>Отзыв CMK (KEK)

Вы можете отозвать доступ к данным, отключив ключ или удалив политику доступа ресурса *Кластер* в Key Vault. Хранилище кластера Log Analytics всегда реагирует на изменения в разрешениях ключей в течение часа или раннее, после чего служба хранилища станет недоступной. Все новые данные, принимаемые в рабочие области и связанные с ресурсом  *Кластер* , удаляются и являются невосстанавливаемыми. Эти данные будут недоступны, а запросы к этим рабочим областям завершатся ошибкой. Ранее полученные данные остаются в хранилище, пока вы не удалите ресурс *Кластер* и рабочие области. Недоступные данные управляются политикой хранения данных и очищаются при достижении окончания срока хранения. 

Полученные данные за последние 14 дней также хранятся в кэше горячего уровня доступа (с поддержкой SSD) для эффективной работы обработчика запросов. Эта операция удаляется из операции отзыва ключа и становится недоступной.

Служба хранилища периодически проверяет Key Vault, чтобы попытаться развернуть ключ шифрования и после доступа к нему в течение 30 минут возобновить прием данных и запрос.

## <a name="cmk-kek-rotation"></a>Смена CMK (KEK)

Для смены CMK требуется явное обновление ресурса *Кластер* с помощью новой версии ключа в Azure Key Vault. Следуйте инструкциям в разделе "Обновление ресурса *Кластер* с подробными сведениями об идентификаторе ключа". Если вы не обновите сведения о новом идентификаторе ключа в ресурсе *Кластер*, хранилище кластера Log Analytics будет использовать предыдущий ключ для шифрования. Если вы отключаете или удаляете старый ключ перед обновлением нового ключа в ресурсе *Кластер*, это приведет к состоянию [отзыв ключа](#cmk-kek-revocation).

После операции смены ключа все данные остаются доступными, так как данные всегда шифруются с помощью ключа шифрования учетной записи (АЕК), а АЕК теперь зашифрован с помощью новой версии ключа шифрования ключей (KEK) в Key Vault.

## <a name="cmk-manage"></a>Управление CMK

- **Получение всех ресурсов *Кластер* для группы ресурсов**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Ответ**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- **Получение всех ресурсов *Кластер* для подписки**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Ответ**
    
  Тот же ответ, что и для "ресурсов *Кластер* для группы ресурсов", но в области подписки.

- **Обновление *резервирования мощности* в ресурсе *Кластер***

  Когда объем данных в связанных рабочих областях изменяется с течением времени и необходимо соответствующим образом обновить уровень резервирования мощности. Следуйте [обновлению ресурса](#update-cluster-resource-with-key-identifier-details) *Кластер* и предоставьте новое значение емкости. Это значение может находиться в диапазоне от 1 000 до 2 000 ГБ в день и его можно изменять шагами по 100 единиц. Чтобы включить уровень выше 2 000 ГБ в день, обратитесь в службу технической поддержки Майкрософт. Обратите внимание, что вы не обязаны предоставлять полный текст REST-запроса и должны включить в него sku:

  **body**
  ```json
  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **Обновление *billingType* в ресурсе *Кластер***

  Свойство *billingType* определяет атрибуты выставления счетов для ресурса *Кластер* и его данных:
  - *кластер* (по умолчанию) — выставление счетов относится к подписке, в которой размещен ваш ресурс "Кластер";
  - *рабочие области* — выставление счетов относится к подпискам, в которых пропорционально размещаются ваши рабочие области.
  
  Следуйте разделу по [обновлению ресурса](#update-cluster-resource-with-key-identifier-details) *Кластер* и предоставьте новое значение billingType. Обратите внимание, что вы не обязаны предоставлять полный текст REST-запроса и должны включить в него *billingType*:

  **body**
  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Отмена связывания рабочей области**

  Для выполнения этой операции необходимы права на "запись" для рабочей области и ресурса *Кластер*. Вы можете отменить связывание рабочей области и вашего ресурса *Кластер* в любое время. Новые принимаемые данные после операции отмены связывания сохраняются в хранилище Log Analytics и шифруются с помощью ключа Майкрософт. Вы можете запросить данные, загруженные в вашу рабочую область до и после отмены связывания, при условии, что ресурс *Кластер* подготовлен и сконфигурирован с помощью действующего ключа Key Vault.

  Этот запрос Resource Manager является асинхронной операцией.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Ответ**

  200 ОК и заголовок.

  После завершения операции отмены связывания, которая может занять 90 минут, полученные данные хранятся в хранилище Log Analytics. Проверить состояние отмены связывания рабочей области можно двумя способами.

  1. Скопируйте значение URL-адреса Azure-AsyncOperation из ответа и выполните [проверку состояния асинхронных операций](#asynchronous-operations-and-status-check).
  2. Отправьте запрос [Рабочие области — получение](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) и оцените ответ. Рабочая область с отмененным связыванием не будет иметь идентификатор *clusterResourceId* в пункте *Компоненты*.

- **Удаление ресурса *Кластер***

  Чтобы выполнить эту операцию, требуются разрешения на "запись" для ресурса *Кластер*. Операция обратимого удаления позволяет восстановить ресурс *Кластер*, включая его данные, в течение 14 дней, независимо от того, было удаление случайным или умышленным. Имя ресурса *Кластер* сохраняется в течение периода обратимого удаления, и вы не сможете создать новый кластер с таким же именем. После периода обратимого удаления имя ресурса *Кластер* освобождается, а ресурс *Кластер* и его данные будут окончательно удалены и вы не сможете их восстановить. При операции удаления связь любой рабочей области с ресурсом *Кластер* будет удалена. Новые принимаемые данные сохраняются в хранилище Log Analytics и шифруются с помощью ключа Майкрософт. Операция, не связанная с рабочими областями, является асинхронной и может занять до 90 минут.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Ответ**

  200 ОК

- **Восстановление данных и ресурса *Кластер*** 
  
  Ресурс *Кластер*, удаленный за последние 14 дней, находится в состоянии обратимого удаления, и его можно восстановить вместе с его данными. Поскольку при удалении ресурса *Кластер* все рабочие области отменяют связь с ресурсом *Кластер*, после восстановления рабочие области необходимо повторно связать, чтобы выполнить шифрование CMK. В настоящее время операция восстановления выполняется вручную группой продуктов. Используйте канал Майкрософт для запросов на восстановление.

## <a name="limitationsandconstraints"></a>Пределы и ограничения

— CMK поддерживается в выделенном кластере Log Analytics и подходит для клиентов, отправляющих 1 ТБ в день или больше.

— Максимальное количество ресурсов  *Кластер*  для каждого региона и подписки равно 2.

— Вы можете связать рабочую область с ресурсом  *Кластер* , а затем отсоединить его, если для рабочей области не требуется CMK. Число ассоциаций рабочей области в определенной рабочей области в течение 30 дней ограничено числом 2.

— Связь рабочей области с ресурсом  *Кластер*  следует выполнять только после проверки завершения подготовки кластера Log Analytics. Данные, отправляемые в рабочую область до завершения, будут удалены и не подлежат восстановлению.

— Шифрование CMK применяется к вновь принимаемым данным после     настройки CMK. Данные, которые были приняты до     конфигурации CMK, остаются зашифрованными с помощью ключа Майкрософт. Вы можете легко запрашивать     данные, полученные до и после создания конфигурации CMK.

— Azure Key Vault должен быть настроен как восстанавливаемый. Эти свойства не включены по умолчанию, и их нужно настроить с помощью интерфейса командной строки или PowerShell:   - [Следует включить ](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)обратимое удаление
     и   - [защиту от очистки](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) , чтобы защититься от принудительного удаления секрета или хранилища даже после обратимого удаления.

Перемещение ресурса - *Кластер*  в другую группу ресурсов или подписку     в настоящее время не поддерживается.

— Ваш Azure Key Vault, ресурс  *Кластер*  и связанные рабочие области должны находиться в одном и том же регионе и в одном клиенте Azure Active Directory (Azure AD), но они могут размещаться в разных подписках.

— Связь рабочей области с ресурсом  *Кластер*  завершится ошибкой, если рабочая область     уже связана с другим ресурсом  *Кластер* .

## <a name="troubleshooting"></a>Устранение неполадок

- Поведение при доступности Key Vault:
  - При нормальных операциях. Служба хранилища кэширует АЕК в течение короткого периода времени и возвращается к Key Vault для периодического разворачивания.
    
  - Нерегулярные ошибки подключений. Служба хранилища обрабатывает нерегулярные ошибки (время ожидания, сбои подключений, проблемы DNS), позволяя ключам оставаться в кэше в течение короткого промежутка времени, что приводит к небольшому перебою в доступности. Возможность создавать запросы и принимать данные не прерывается.
    
  - Действующий сайт. Недоступность в течение 30 минут приведет к тому, что учетная запись хранения станет недоступной. Создание запросов недоступно, а полученные данные кэшируются в течение нескольких часов с помощью ключа Майкрософт, чтобы избежать потери данных. При восстановлении доступа к Key Vault запрос становится доступным, а временные кэшированные данные принимаются в хранилище данных и шифруются с помощью CMK.

  - Частота доступа Key Vault. Частота, с которой Azure Monitor получает доступ к хранилищу Key Vault для операций упаковки и распаковки, составляет от 6 до 60 секунд.

- Если вы создадите ресурс *Кластер* и сразу же укажете KeyVaultProperties, операция может завершиться ошибкой, поскольку политику доступа нельзя определить, пока системное удостоверение не будет назначено ресурсу *Кластер*.

- Если вы обновляете существующий ресурс *Кластер* с помощью KeyVaultProperties и в Key Vault отсутствует политика доступа к ключам действия "Получить", операция завершится ошибкой.

- При попытке удалить ресурс *Кластер*, связанный с рабочей областью, операция удаления завершится ошибкой.

- Если при создании ресурса *Кластер* возникает конфликт, возможно, вы удалили ресурс *Кластер* в течение последних 14 дней и они не входят в период обратимого удаления. Имя ресурса *Кластер* сохраняется в течение периода обратимого удаления, и вы не сможете создать новый кластер с таким же именем. Имя освобождается после периода обратимого удаления, когда ресурс *Кластер* окончательно удаляется.

- Если вы обновляете ресурс *Кластер* во время выполнения операции, операция завершится ошибкой.

- Если вам не удается развернуть ресурс *Кластер*, убедитесь, что Azure Key Vault, ресурс  *Кластер*  и связанные рабочие области Log Analytics находятся в одном регионе. Они могут находиться в разных подписках.

- Если вы обновляете версию ключа в Key Vault и не обновляете сведения о новом идентификаторе ключа в ресурсе *Кластер*, кластер Log Analytics будет использовать предыдущий ключ и ваши данные станут недоступными. Обновите сведения о новом идентификаторе ключа в ресурсе *Кластер*, чтобы возобновить прием данных и получить возможность запрашивать данные.

- Чтобы получить поддержку и справку, связанные с ключом, управляемым клиентом, используйте свои контактные данные в Майкрософт.
