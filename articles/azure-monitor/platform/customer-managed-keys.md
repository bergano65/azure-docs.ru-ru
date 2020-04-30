---
title: Azure Monitor конфигурация ключа, управляемого клиентом
description: Сведения и действия по настройке ключа, управляемого клиентом (CMK), для шифрования данных в рабочих областях Log Analytics с помощью ключа Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758803"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor конфигурация ключа, управляемого клиентом 

В этой статье содержатся общие сведения и инструкции по настройке ключей, управляемых клиентом (CMK), для рабочих областей Log Analytics и компонентов Application Insights. После настройки все данные, отправленные в рабочие области или компоненты, шифруются с помощью ключа Azure Key Vault.

Перед настройкой рекомендуется ознакомиться [с ограничениями и ограничениями,](#limitations-and-constraints) приведенными ниже.

## <a name="disclaimers"></a>Заявления об отказе от ответственности

- Azure Monitor CMK — это функция раннего доступа, которая включена для зарегистрированных подписок.

- Развертывание CMK, описанное в этой статье, распространяется в рабочее качество и поддерживается таким образом, хотя оно является функцией раннего доступа.

- Возможность CMK поставляется в выделенном кластере хранилища данных, который является кластером Azure обозреватель данных (ADX) и подходит для клиентов, отправляющих 1 ТБ в день или более. 

- Модель ценообразования CMK сейчас недоступна, и она не рассматривается в этой статье. Модель ценообразования для выделенного кластера ADX ожидается во втором квартале календарного года (CY) 2020 и будет применяться к любым существующим развертываниям CMK.

- В этой статье описывается конфигурация CMK для рабочих областей Log Analytics. CMK для компонентов Application Insights также поддерживается в этой статье, а различия перечислены в приложении.

> [!NOTE]
> Log Analytics и Application Insights используют одну и ту же платформу хранилища данных и обработчик запросов.
> Мы объединяем эти два хранилища вместе с помощью интеграции Application Insights в Log Analytics, чтобы создать единое единое хранилище журналов в Azure Monitor. Это изменение запланировано на второй квартал календарного года 2020. Если вам не нужно развертывать CMK для данных Application Insights, рекомендуется ожидать завершения консолидации, так как такие развертывания будут нарушены консолидацией, и после миграции в Log Analytics рабочую область потребуется повторно настроить CMK. Минимальное значение 1 ТБ в день применяется на уровне кластера и до завершения консолидации в течение второго квартала Application Insights и Log Analytics требуются отдельные кластеры.

## <a name="customer-managed-key-cmk-overview"></a>Обзор ключа, управляемого клиентом (CMK)

[Шифрование неактивных](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) данных — это общие требования к конфиденциальности и безопасности в организациях. Вы можете полностью управлять шифрованием в Azure, в то время как у вас есть различные варианты для тесного управления шифрованием или ключами шифрования.

Хранилище данных Azure Monitor гарантирует, что все данные шифруются с помощью ключей, управляемых Azure, и хранятся в службе хранилища Azure. Azure Monitor также предоставляет возможность шифрования данных с помощью собственного ключа, хранящегося в [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), доступ к которому осуществляется с помощью [управляемой системой идентификации](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) с проверкой подлинности. Этот ключ может быть [программным или аппаратным обеспечением с защитой HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Azure Monitor использование шифрования идентично тому, как работает шифрование службы [хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

Частота, с которой Azure Monitor доступ к хранилищу Key Vault для операций упаковки и распаковки, составляет от 6 до 60 секунд.Azure Monitor хранилище всегда учитывает изменения в ключевых разрешениях в течение часа.

Полученные данные за последние 14 дней также сохраняются в оперативном кэше (с поддержкой SSD) для эффективной работы механизма запросов. Эти данные остаются зашифрованными с помощью ключей Майкрософт независимо от конфигурации CMK, но мы работаем над тем, чтобы диск SSD был зашифрован с помощью CMK в первой половине 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Как работает CMK в Azure Monitor

Azure Monitor использует управляемое системой удостоверение для предоставления доступа к Azure Key Vault.Управляемое системой удостоверение может быть связано только с одним ресурсом Azure. Удостоверение хранилища данных Azure Monitor (кластер ADX) поддерживается на уровне кластера, и это указывает на то, что возможность CMK доставляется в выделенный кластер ADX. Для поддержки CMK в нескольких рабочих областях новый ресурс Log Analytics (*кластер*) выполняет в качестве промежуточного подключения между Key Vaultом и рабочими областями log Analytics. Эта концепция соответствует ограничению удостоверения, назначенного системой, и удостоверение сохраняется между кластером ADX и ресурсом *кластера* log Analytics, а данные всех связанных рабочих областей защищаются с помощью ключа Key Vault. Хранилище кластера ундерлай ADX использует управляемое удостоверение,\'связанное с ресурсом *кластера* для проверки подлинности и доступа к Azure Key Vault через Azure Active Directory.

![Обзор CMK](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Key Vault клиента.
2.    Ресурс *кластера* log Analytics клиента с правами управляемого удостоверения с разрешениями на Key Vault — удостоверение поддерживается на уровне хранилища данных (ADX Cluster).
3.    Azure Monitor выделенный кластер ADX.
4.    Рабочие области клиента, связанные с ресурсом *кластера* для шифрования CMK.

## <a name="encryption-keys-management"></a>Управление ключами шифрования

Существует 3 типа ключей, участвующих в шифровании данных хранилища:

- Ключ шифрования **KEK** (CMK)
- **АЕК** — ключ шифрования учетной записи
- **DEK** — ключ шифрования данных

Применяются следующие правила.

- Учетные записи хранения ADX создают уникальный ключ шифрования для каждой учетной записи хранения, которая называется АЕК.

- АЕК используется для получения DEK, которые являются ключами, используемыми для шифрования каждого блока данных, записываемых на диск.

- Когда вы настраиваете ключ в Key Vault и ссылаетсяе на него в ресурсе *кластера* , служба хранилища Azure отправляет запросы в ваш Azure Key Vault для переноса и распаковки АЕК для выполнения операций шифрования и расшифровки данных.

- KEK никогда не покидает ваш Key Vault и в случае ключа HSM он никогда не покидает оборудование.

- Служба хранилища Azure использует управляемое удостоверение, связанное с ресурсом *кластера* , для проверки подлинности и доступа к Azure Key Vault через Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Процедура подготовки CMK

Для Application Insights конфигурации CMK Используйте содержимое приложения для шагов 3 и 6.

1. Список разрешений подписки — это требуется для этого компонента раннего доступа
2. Создание Azure Key Vault и хранение ключа
3. Создание ресурса *кластера*
4. Подготовка Azure Monitor хранилища данных (кластер ADX)
5. Предоставление разрешений Key Vault
6. Связывание рабочих областей Log Analytics

В настоящее время процедура не поддерживается в пользовательском интерфейсе, а процесс подготовки выполняется с помощью REST API.

> [!IMPORTANT]
> Любой запрос API должен содержать маркер авторизации носителя в заголовке запроса.

Пример:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Где *eyJ0eXAiO...* представляет полный маркер авторизации. 

Получить маркер можно с помощью одного из следующих методов:

1. Используйте метод [Регистрация приложений](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) .
2. На портале Azure
    1. Переход к портал Azure в "инструменте разработчика" (F12)
    1. Найдите строку авторизации в разделе "заголовки запросов" в одном из экземпляров "Пакетная обработка API-версия". Он выглядит следующим образом: "Authorization: Bearer eyJ0eXAiO....". 
    1. Скопируйте и добавьте его в вызов API в приведенных ниже примерах.
3. Перейдите на сайт документации по Azure RESTFUL. Нажмите кнопку "попробовать" для любого API и скопируйте токен носителя.

### <a name="asynchronous-operations-and-status-check"></a>Асинхронные операции и проверка состояния

Некоторые операции в этой процедуре настройки выполняются асинхронно, так как они не могут быть завершены быстро. Ответ для асинхронной операции изначально возвращает код состояния HTTP 200 (ОК) и заголовок со свойством *Azure-AsyncOperation* , если оно принято:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Вы можете проверить состояние асинхронной операции, отправив запрос GET в значение заголовка *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

Ответ содержит сведения об операции и ее *состояние*. Это может быть одно из следующих:

Операция выполняется
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
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

Сбой операции
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

Возможность CMK — это функция раннего доступа. Подписки, в которых планируется создавать ресурсы *кластера* , должны быть предварительно список разрешений группой продуктов Azure. Используйте свои контакты в Майкрософт, чтобы предоставить идентификаторы подписок.

> [!IMPORTANT]
> Функция CMK является региональной. Azure Key Vault, ресурс *кластера* и связанные рабочие области log Analytics должны находиться в одном регионе, но они могут находиться в разных подписках.

### <a name="storing-encryption-key-kek"></a>Хранение ключа шифрования (KEK)

Создайте или используйте Azure Key Vault, которые уже необходимо создать, или импортируйте ключ, который будет использоваться для шифрования данных. Azure Key Vault должны быть настроены как восстанавливаемые для защиты ключа и доступа к данным в Azure Monitor. Эту конфигурацию можно проверить в разделе "Свойства" в Key Vault, чтобы включить *обратимое удаление* и *защиту от* удаления.

![Параметры обратимого удаления и очистки защиты](media/customer-managed-keys/soft-purge-protection.png)

Эти параметры доступны через CLI и PowerShell.
- [обратимое удаление](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Очистка защиты](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) защищает от принудительного удаления секрета или хранилища даже после обратимого удаления

### <a name="create-cluster-resource"></a>Создание ресурса *кластера*

Этот ресурс используется в качестве промежуточного подключения между Key Vault и рабочими областями Log Analytics. После получения подтверждения о том, что ваши подписки были список разрешений, создайте ресурс *кластера* log Analytics в регионе, где находятся ваши рабочие области. Для Application Insights и Log Analytics требуются отдельные типы ресурсов *кластера* . Тип ресурса *кластера* определяется во время создания, присвоив свойству *clusterType* значение *LogAnalytics*или *ApplicationInsights*. Тип ресурса кластера нельзя изменить после.

Для Application Insights конфигурации CMK Используйте содержимое приложения.

При создании ресурса *кластера* необходимо указать уровень резервирования емкости (SKU). Уровень резервирования емкости может быть в диапазоне от 1 000 до 2 000 ГБ в день. его можно обновить, выполнив действия, описанные в разделе 100. Если требуется уровень резервирования емкостью более 2 000 ГБ в день, обратитесь к своему контакту Майкрософт, чтобы включить его. Это свойство не влияет на выставление счетов. в настоящее время используется модель ценообразования для выделенного кластера, поэтому выставление счетов будет применяться ко всем существующим CMK развертываниям.

**Создать**

Этот диспетчер ресурсов запрос является асинхронной операцией.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
Удостоверение назначается ресурсу *кластера* во время создания.

**Ответ**

200 ОК и заголовок.
В течение периода раннего доступа функции кластер ADX подготавливается вручную. Несмотря на то, что подготовка кластера к работе выполняется в ADX, можно проверить состояние подготовки двумя способами.
1. Скопируйте значение URL-адреса Azure-AsyncOperation из ответа и выполните [проверку состояния асинхронных операций](#asynchronous-operations-and-status-check).
2. Отправьте запрос GET на ресурс *кластера* и просмотрите значение *provisioningState* . Это *провисионингаккаунт* во время подготовки и *успешного* завершения.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Подготовка Azure Monitor хранилища данных (кластер ADX)

В течение периода раннего доступа функции кластер ADX подготавливается группой разработчиков вручную после выполнения предыдущих шагов. Используйте канал Майкрософт для этого шага и укажите ответ на ресурс *кластера* . 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

Идентификатор GUID "principalId" создается управляемой службой удостоверений для ресурса *кластера* .

### <a name="grant-key-vault-permissions"></a>Предоставление Key Vault разрешений

Обновите Key Vault с помощью новой политики доступа, которая предоставляет разрешения для ресурса *кластера* . Эти разрешения используются хранилищем Azure Monitor ундерлай для шифрования данных. Откройте Key Vault в портал Azure и щелкните "политики доступа", а затем "+ добавить политику доступа", чтобы создать политику с этими параметрами:

- Разрешения ключа: выберите "Get", "обернуть ключ" и "разворачивать ключ".
- Выбор субъекта: введите значение идентификатора участника, которое было возвращено в ответе на предыдущем шаге.

![предоставление Key Vault разрешений](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Разрешение *Get* необходимо для проверки того, что Key Vault настроена как восстанавливаемая для защиты ключа и доступа к данным Azure Monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Обновление ресурса кластера с подробными сведениями об идентификаторе ключа

Этот шаг выполняется во время начальной и будущей обновлений версии ключа в Key Vault. Он информирует Azure Monitor хранилище о версии ключа, используемой для шифрования данных. При обновлении новый ключ используется для переноса и распаковки ключа хранилища (АЕК).

Чтобы обновить ресурс *кластера* , используя сведения об *идентификаторе ключа* Key Vault, выберите текущую версию ключа в Azure Key Vault, чтобы получить сведения об идентификаторе ключа.

![Предоставление Key Vault разрешений](media/customer-managed-keys/key-identifier-8bit.png)

Обновите ресурс *кластера* кэйваултпропертиес, используя сведения об идентификаторе ключа.

**Обновление**

Этот диспетчер ресурсов запрос является асинхронной операцией при обновлении сведений об идентификаторе ключа, а также в синхронном режиме при обновлении значения емкости.

> [!Warning]
> Необходимо указать полный текст в обновлении ресурса *кластера* , который включает в себя *удостоверение*, *SKU*, *кэйваултпропертиес* и *Расположение*. Отсутствие сведений о *кэйваултпропертиес* приведет к удалению идентификатора ключа из ресурса *кластера* и вызову [отзыва ключа](#cmk-kek-revocation).

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
     "capacity": 1000
     },
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>"
}
```
"Кэйваултпропертиес" содержит сведения об идентификаторе ключа Key Vault.

**Ответ**

200 ОК и заголовок.
Это занимает несколько минут для завершения распространения идентификатора ключа. Проверить состояние обновления можно двумя способами.
1. Скопируйте значение URL-адреса Azure-AsyncOperation из ответа и выполните [проверку состояния асинхронных операций](#asynchronous-operations-and-status-check).
2. Отправьте запрос GET на ресурс *кластера* и просмотрите свойства *кэйваултпропертиес* . Недавно обновленные сведения об идентификаторе ключа должны возвращаться в ответе.

Ответ на запрос GET для ресурса *кластера* должен выглядеть следующим образом после завершения обновления идентификатора ключа:

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Сопоставление рабочей области с ресурсом *кластера*
Для Application Insights конфигурации CMK Используйте содержимое приложения для этого шага.

Для выполнения этой операции требуются разрешения "запись" для рабочей области и ресурса *кластера* , в том числе следующие действия:

- Рабочая область: Microsoft. OperationalInsights/рабочие области/запись
- В ресурсе *кластера* : Microsoft. OperationalInsights/Clusters/Write

> [!IMPORTANT]
> Этот шаг следует выполнять только после подготовки кластера ADX. Если вы связываете рабочие области и принимаете данные до подготовки, полученные данные будут удалены и не будут восстановлены.

**Связывание рабочей области**

Этот диспетчер ресурсов запрос является асинхронной операцией.

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

200 ОК и заголовок.
Полученные данные хранятся в зашифрованном виде с помощью управляемого ключа после операции сопоставления, что может занять до 90 минут. Проверить состояние сопоставления рабочей области можно двумя способами.
1. Скопируйте значение URL-адреса Azure-AsyncOperation из ответа и выполните [проверку состояния асинхронных операций](#asynchronous-operations-and-status-check).
2. Отправить [рабочие области — запрос Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) и наблюдать за ответом, связанная Рабочая область будет иметь клустерресаурцеид в разделе "компоненты".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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
    "retentionInDays": days,
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

## <a name="cmk-kek-revocation"></a>Отзыв CMK (KEK)

Вы можете отозвать доступ к данным, отключив ключ или удалив политику доступа к ресурсам *кластера* в Key Vault. Azure Monitor хранилище всегда будет учитывать изменения ключевых разрешений в течение часа, обычно быстрее, и хранилище станет недоступным. Все данные, принимаемые в рабочие области, связанные с ресурсом *кластера* , удаляются, и запросы завершатся ошибкой. Ранее полученные данные остаются недоступными в хранилище Azure Monitor, пока вы работаете с *кластерным* ресурсом, и ваши рабочие области не удаляются. Недоступные данные определяются политикой хранения данных и удаляются при достижении срока хранения.

Хранилище периодически опрашивает Key Vault, чтобы попытаться расшифровать ключ шифрования и получить доступ к нему, а затем возобновить прием данных и выполнить запрос в течение 30 минут.

## <a name="cmk-kek-rotation"></a>Вращение CMK (KEK)

Для вращения CMK требуется явное обновление ресурса *кластера* с новой версией ключа в Azure Key Vault. Чтобы обновить Azure Monitor с помощью новой версии ключа, следуйте инструкциям в разделе "Обновление ресурса *кластера* с подробными сведениями об идентификаторе ключа". Если вы обновляете версию ключа в Key Vault и не обновляете новые сведения об идентификаторе ключа в ресурсе *кластера* , Azure Monitor хранилище будет использовать предыдущий ключ.
Все данные доступны после операции смены ключей, включая данные, полученные до смены ключа, так как все данные остаются зашифрованными ключом шифрования учетной записи (АЕК), а АЕК теперь шифруется с помощью новой версии ключа шифрования ключа (KEK).

## <a name="limitations-and-constraints"></a>Ограничения и ограничения

- Функция CMK поддерживается на уровне кластера ADX и требует выделенного кластера Azure Monitor ADX с требованием отправки 1 ТБ в день или более.

- Максимальное число ресурсов *кластера* на подписку ограничено 2

- Связь ресурсов *кластера* с рабочей областью должна быть перенесена только после того, как была выполнена подготовка кластера ADX. Данные, отправляемые в рабочую область до завершения подготовки, будут удалены и не будут восстановлены.

- Шифрование CMK применяется к вновь принимаемым данным после настройки CMK. Данные, которые были приняты до настройки CMK, остаются зашифрованными с помощью ключа Microsoft Key. Вы можете легко запрашивать данные, полученные до и после настройки CMK.

- Вы можете отменить связывание рабочей области с ресурсом *кластера* , когда решите, что CMK не требуется для конкретной рабочей области. Новые принимаемые данные после операции отмены ассоциации хранятся в общем хранилище Log Analytics, которое было до того, как оно было связано с ресурсом *кластера* . Вы можете запрашивать данные, полученные до и после отмены взаимосвязи, без проблем, если ресурс *кластера* подготовлен и настроен с допустимым ключом key Vault.

- Azure Key Vault должны быть настроены как восстанавливаемые. Эти свойства не включены по умолчанию и должны быть настроены с помощью интерфейса командной строки или PowerShell:

  - Необходимо включить [обратимое удаление](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - Чтобы защититься от принудительного удаления секрета или хранилища даже после обратимого удаления, необходимо включить [защиту](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) от удаления.

- Для Application Insights и Log Analytics требуются отдельные ресурсы *кластера* . Тип ресурса *кластера* определяется во время создания, присвоив свойству "clusterType" значение "LogAnalytics" или "ApplicationInsights". Невозможно изменить тип ресурса *кластера* .

- Перемещение ресурса *кластера* в другую группу ресурсов или подписку сейчас не поддерживается.

- Ваша Azure Key Vault, *кластерный* ресурс и связанные рабочие области должны находиться в одном и том же регионе и в одном клиенте Azure Active Directory (Azure AD), но они могут находиться в разных подписках.

- Связь рабочей области с ресурсом *кластера* завершится ошибкой, если она связана с другим ресурсом *кластера* .

## <a name="troubleshooting-and-management"></a>Устранение неполадок и управление

- Доступность Key Vault
    - В нормальных операциях — кэш хранилища АЕК в течение короткого периода времени и возвращается к Key Vault для периодического растекания.
    
    - Ошибки неустранимых подключений. хранилище обрабатывает временные ошибки (время ожидания, сбои подключений, проблемы DNS), позволяя ключам оставаться в кэше в течение короткого промежутка времени, а это приводит к небольшому нестабильной работеу доступности. Возможности запросов и приема продолжаются без перерывов.
    
    - Live site — недоступность в течение 30 минут приведет к тому, что учетная запись хранения станет доступной. Возможность запросов недоступна, и полученные данные кэшируются в течение нескольких часов с помощью ключа Майкрософт, чтобы избежать потери данных. При восстановлении доступа к Key Vault запрос становится доступным, а временные кэшированные данные принимаются в хранилище данных и шифруются с помощью CMK.

- Если вы создадите ресурс *кластера* и сразу укажете кэйваултпропертиес, операция может завершиться ошибкой, так как политика доступа не может быть определена, пока не будет назначено системное удостоверение ресурсу *кластера* .

- Если вы обновляете существующий ресурс *кластера* с помощью кэйваултпропертиес и в Key Vault отсутствует политика доступа к ключам "Get", операция завершится ошибкой.

- При попытке удалить ресурс *кластера* , связанный с рабочей областью, операция удаления завершится ошибкой.

- Если при создании ресурса *кластера* возникает ошибка конфликта, возможно, вы удалили ресурс *кластера* за последние 14 дней и в течение периода обратимого удаления. Имя ресурса *кластера* остается зарезервированным во время периода обратимого удаления, и вы не можете создать новый кластер с таким именем. Имя освобождается после периода обратимого удаления при окончательном удалении ресурса *кластера* .

- Получить все ресурсы *кластера* для группы ресурсов:

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
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- Получить все ресурсы *кластера* для подписки:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Ответ**
    
  Тот же ответ, что и для "ресурсов*кластера* для группы ресурсов", но в области подписки.

- Обновление *резервирования емкости* в *кластерном* ресурсе. при изменении объема данных в связанных рабочих областях и необходимости обновления уровня резервирования емкости для рекомендаций по выставлению счетов используйте [ресурс обновления *кластера* ](#update-cluster-resource-with-key-identifier-details) и укажите новое значение емкости. Уровень резервирования емкости может быть в диапазоне от 1 000 до 2 000 ГБ в день и в шагах 100. Чтобы включить уровень выше 2 000 ГБ в день, обратитесь к своему контакту Майкрософт.

- Удаление ресурса *кластера* . операция обратимого удаления выполняется, чтобы разрешить восстановление ресурса *кластера* , включая его данные в течение 14 дней, независимо от того, было ли удаление случайным или умышленно. Имя ресурса *кластера* остается зарезервированным во время периода обратимого удаления, и вы не можете создать новый кластер с таким именем. После периода обратимого удаления имя ресурса *кластера* освобождается, ресурс *кластера* и данные будут окончательно удалены и не будут восстановлены. Любая связанная Рабочая область становится несвязанной с ресурсом *кластера* при операции удаления. Новые принимаемые данные хранятся в общем хранилище Log Analytics и шифруются с помощью ключа Microsoft. Операция отмены, связанная с рабочими областями, является асинхронной.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Ответ**

  200 ОК

- Восстановление ресурса *кластера* и данных. *кластерный* ресурс, который был удален за последние 14 дней, находится в состоянии обратимого удаления и может быть восстановлен. Эта группа продуктов выполняется вручную в настоящее время. Используйте канал Майкрософт для запросов на восстановление.

## <a name="appendix"></a>Приложение

Application Insights также поддерживается управляемый ключ клиента (CMK), однако следует учесть следующее изменение, которое поможет спланировать развертывание CMK для компонентов Application Insight.

Log Analytics и Application Insights используют одну и ту же платформу хранилища данных и обработчик запросов. Мы объединяем эти два хранилища вместе с помощью интеграции Application Insights в Log Analytics, чтобы предоставить единое хранилище журналов в Azure Monitor за второй квартал
2020. Это изменение приведет к внесению данных Application Insights в Log Analytics рабочие области и сделать запросы, аналитические данные и другие улучшения, которые могут быть доступны в процессе настройки CMK в рабочей области, также будут применяться к данным Application Insights.

> [!NOTE]
> Если вам не нужно развертывать CMK для данных Application Insights до интеграции, рекомендуется подождать с Application Insights CMK, так как такие развертывания будут нарушены интеграцией, и потребуется повторно настроить CMK после миграции в Log Analytics рабочую область. Минимальное значение 1 ТБ в день применяется на уровне кластера и до завершения консолидации в течение второго квартала Application Insights и Log Analytics требуются отдельные кластеры.

## <a name="application-insights-cmk-configuration"></a>Конфигурация Application Insights CMK

Конфигурация Application Insights CMK идентична процессу, показанному в этой статье, включая ограничения и устранение неполадок, за исключением следующих шагов:

- Создание ресурса *кластера*
- Связывание компонента с ресурсом *кластера*

При настройке CMK для Application Insights используйте следующие действия вместо перечисленных выше.

### <a name="create-a-cluster-resource"></a>Создание ресурса *кластера*

Этот ресурс используется в качестве промежуточного подключения между Key Vault и компонентами. После получения подтверждения о том, что ваши подписки были список разрешений, создайте ресурс *кластера* log Analytics в регионе, где находятся компоненты. Тип ресурса *кластера* определяется во время создания, присвоив свойству *clusterType* значение *LogAnalytics*или *ApplicationInsights*. Он должен быть *ApplicationInsights* для Application Insights CMK. Параметр *clusterType* не может быть изменен после настройки.

**Создать**

Этот диспетчер ресурсов запрос является асинхронной операцией.

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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**Ответ**

200 ОК и заголовок.
В течение периода раннего доступа функции кластер ADX подготавливается вручную. Несмотря на то, что подготовка кластера к работе выполняется в ADX, можно проверить состояние подготовки двумя способами.
1. Скопируйте значение URL-адреса Azure-AsyncOperation из ответа и выполните [проверку состояния асинхронных операций](#asynchronous-operations-and-status-check).
2. Отправьте запрос GET на ресурс *кластера* и просмотрите значение *provisioningState* . Это *провисионингаккаунт* во время подготовки и *успешного* завершения.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Связывание компонента с ресурсом *кластера* с помощью [компонентов — создание или обновление](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

Для выполнения этой операции необходимо иметь разрешения на запись как для компонента, так и для ресурса *кластера* , в том числе следующие действия:

- В компоненте: Microsoft. Insights/компонент/запись
- В ресурсе *кластера* : Microsoft. OperationalInsights/Clusters/Write

> [!IMPORTANT]
> Этот шаг следует выполнять только после подготовки кластера ADX. Если связать компоненты и принять данные до подготовки, полученные данные будут удалены и не будут восстановлены.
> Чтобы убедиться, что кластер ADX подготовлен, выполните REST API получения ресурса *кластера* и убедитесь, что значение *provisioningState* *выполнено*.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> Скопируйте и сохраним ответ, так как он понадобится вам в следующих шагах.

**Связывание компонента**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"Клустердефинитионид" — это значение "clusterId", указанное в ответе на предыдущем шаге.
Пример "Kind" — "Web".

**Ответ**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"Клустердефинитионид" — это идентификатор ресурса *кластера* , связанный с этим компонентом.

После сопоставления данные, отправляемые в компоненты, сохраняются в зашифрованном виде с помощью управляемого ключа.
