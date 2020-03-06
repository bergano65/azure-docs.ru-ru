---
title: Azure Monitor конфигурация ключа, управляемого клиентом
description: Сведения и действия по настройке ключа, управляемого клиентом (CMK), для шифрования данных в рабочих областях Log Analytics с помощью ключа Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 02/24/2020
ms.openlocfilehash: d14b4a3f4c3fdddac64596760fdbbfefce49036a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364400"
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
> Мы объединяем эти два хранилища вместе с помощью интеграции Application Insights в Log Analytics, чтобы создать единое единое хранилище журналов в Azure Monitor. Это изменение запланировано на второй квартал календарного года 2020. Если вам не нужно развертывать CMK для данных Application Insights, мы рекомендуем дождаться завершения консолидации, так как такие развертывания будут нарушены консолидацией, и потребуется повторно настроить CMK после миграции в журнал. Рабочая область "аналитика". Минимальное значение 1 ТБ в день применяется на уровне кластера и до завершения консолидации в течение второго квартала Application Insights и Log Analytics требуются отдельные кластеры.

## <a name="customer-managed-key-cmk-overview"></a>Обзор ключа, управляемого клиентом (CMK)

[Шифрование неактивных](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) данных — это общие требования к конфиденциальности и безопасности в организациях. Вы можете полностью управлять шифрованием в Azure, в то время как у вас есть различные варианты для тесного управления шифрованием или ключами шифрования.

Хранилище данных Azure Monitor гарантирует, что все данные шифруются с помощью ключей, управляемых Azure, и хранятся в службе хранилища Azure. Azure Monitor также предоставляет возможность шифрования данных с помощью собственного ключа, хранящегося в [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), доступ к которому осуществляется с помощью [управляемой системой идентификации](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) с проверкой подлинности. Этот ключ может быть [программным или аппаратным обеспечением с защитой HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Azure Monitor использование шифрования идентично тому, как работает шифрование службы [хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

Частота, с которой Azure Monitor доступ к хранилищу Key Vault для операций упаковки и распаковки, составляет от 6 до 60 секунд. Azure Monitor хранилище всегда учитывает изменения в ключевых разрешениях в течение часа.

Полученные данные за последние 14 дней также сохраняются в оперативном кэше (с поддержкой SSD) для эффективной работы механизма запросов. Эти данные остаются зашифрованными с помощью ключей Майкрософт независимо от конфигурации CMK, но мы работаем над тем, чтобы SSD был зашифрован с CMK раннего 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Как работает CMK в Azure Monitor

Azure Monitor использует управляемое системой удостоверение для предоставления доступа к Azure Key Vault. Управляемое системой удостоверение может быть связано только с одним ресурсом Azure. Удостоверение хранилища данных Azure Monitor (кластер ADX) поддерживается на уровне кластера, и это указывает на то, что возможность CMK доставляется в выделенный кластер ADX. Для поддержки CMK в нескольких рабочих областях новый ресурс Log Analytics (*кластер*) выполняет в качестве промежуточного подключения между Key Vaultом и рабочими областями log Analytics. Эта концепция соответствует ограничению удостоверения, назначенного системой, и удостоверение сохраняется между кластером ADX и ресурсом *кластера* log Analytics, а данные всех связанных рабочих областей защищаются с помощью ключа Key Vault. Хранилище кластера ундерлай ADX использует управляемое удостоверение\', связанное с ресурсом *кластера* для проверки подлинности и доступа к Azure Key Vault через Azure Active Directory.

![Обзор CMK](media/customer-managed-keys/cmk-overview.png)
1.  Key Vault клиента.
2.  Ресурс кластера Log Analytics клиента с правами управляемого удостоверения с разрешениями на Key Vault — удостоверение поддерживается на уровне хранилища данных (ADX Cluster).
3.  Azure Monitor выделенный кластер ADX.
4.  Рабочие области клиента, связанные с ресурсом кластера для шифрования CMK.

## <a name="encryption-keys-management"></a>Управление ключами шифрования

Существует 3 типа ключей, участвующих в шифровании данных хранилища:

- **KEK** ключ шифрования в Key Vault (CMK)
- **АЕК** — ключ шифрования учетной записи
- **DEK** — ключ шифрования данных

Применяются следующие правила.

- Учетная запись хранения ADX создает уникальный ключ шифрования для каждой учетной записи хранения, который называется АЕК.

- АЕК используется для получения DEK, которые являются ключами, используемыми для шифрования каждого блока данных, записываемых на диск.

- Когда вы настраиваете ключ в Key Vault и ссылаетсяе на него в ресурсе *кластера* , служба хранилища Azure заключает АЕК в KEK в Azure Key Vault.

- KEK никогда не покидает ваш Key Vault и в случае ключа HSM он никогда не покидает оборудование.

- Служба хранилища Azure использует управляемое удостоверение, связанное с ресурсом *кластера* , для проверки подлинности и доступа к Azure Key Vault через Azure Active Directory.

- Для операций чтения и записи служба хранилища Azure отправляет запросы в Azure Key Vault для переноса и распаковки АЕК для выполнения операций шифрования и расшифровки.

## <a name="cmk-provisioning-procedure"></a>Процедура подготовки CMK

Для Application Insights конфигурации CMK Используйте содержимое приложения для шагов 3 и 6.

1. Список разрешений подписки — это требуется для этого компонента раннего доступа
2. Создание Azure Key Vault и хранение ключа
3. Создание ресурса *кластера*
4. Подготовка Azure Monitor хранилища данных (кластер ADX)
5. Предоставление разрешений Key Vault
6. Сопоставление рабочих областей Log Analytics

В настоящее время процедура не поддерживается в пользовательском интерфейсе, а процесс подготовки выполняется с помощью REST API.

> [!IMPORTANT]
> Любой запрос API должен содержать маркер авторизации носителя в заголовке запроса.

Пример:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Где *eyJ0eXAiO...* представляет полный маркер авторизации. 

Получить маркер можно с помощью одного из следующих методов:

1. Используйте метод [Регистрация приложений](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) .

2. На портале Azure
    1. Перейдите к портал Azure в "инструменте разработчика (F12)"
    1. Найдите строку авторизации в разделе "заголовки запросов" в одном из экземпляров "Пакетная обработка API-версия". Он выглядит следующим образом: "Authorization: Bearer \<Token\>". 
    1. Скопируйте и добавьте его в вызов API в приведенных ниже примерах.

3. Перейдите на сайт документации по Azure RESTFUL. Нажмите кнопку "попробовать" для любого API и скопируйте токен носителя.

### <a name="subscription-whitelisting"></a>Список разрешений подписки

Возможность CMK — это функция раннего доступа. Подписки, в которых планируется создавать ресурсы *кластера* , должны быть предварительно список разрешений группой продуктов Azure. Используйте свои контакты в Майкрософт, чтобы предоставить идентификаторы подписок.

> [!IMPORTANT]
> Функция CMK является региональной. Azure Key Vault, учетная запись хранения, ресурс *кластера* и связанные рабочие области log Analytics должны находиться в одном регионе, но они могут находиться в разных подписках.

### <a name="storing-encryption-key-kek"></a>Хранение ключа шифрования (KEK)

Создайте ресурс Azure Key Vault, а затем создайте или импортируйте ключ, который будет использоваться для шифрования данных.

Azure Key Vault должны быть настроены как восстанавливаемые для защиты ключа и доступа к данным Azure Monitor.

Эти параметры доступны через CLI и PowerShell.
- Необходимо включить [обратимое удаление](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- Чтобы защититься от принудительного удаления секрета или хранилища даже после обратимого удаления, необходимо включить [защиту](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) от удаления.

### <a name="create-cluster-resource"></a>Создание ресурса *кластера*

Этот ресурс используется в качестве промежуточного подключения удостоверения между вашей Key Vault и рабочими областями. После получения подтверждения о том, что ваши подписки были список разрешений, создайте ресурс *кластера* log Analytics в регионе, где находятся ваши рабочие области. Для Application Insights и Log Analytics требуются отдельные ресурсы кластера. Тип ресурса *кластера* определяется во время создания, присвоив свойству "clusterType" значение "LogAnalytics" или "ApplicationInsights". Невозможно изменить тип ресурса кластера.

Для Application Insights конфигурации CMK Используйте содержимое приложения для этого шага.

**Создание**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```
Удостоверение назначается ресурсу *кластера* во время создания.
значение "clusterType" — "ApplicationInsights" для Application Insights CMK.

**Ответ**

202 принят. Это стандартный ответ диспетчер ресурсов для асинхронных операций.

Если вы хотите удалить ресурс *кластера* по какой-либо причине, например, создайте его с другим именем или clusterType, используйте следующий REST API.

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Подготовка Azure Monitor хранилища данных (кластер ADX)

В течение периода раннего доступа функции кластер ADX подготавливается группой разработчиков вручную после выполнения предыдущих шагов. Используйте канал Майкрософт для предоставления сведений о ресурсах *кластера* . Скопируйте ответ JSON из ресурса *кластера* Get REST API.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
  "properties": {
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

"Principal-ID" — это идентификатор GUID, созданный управляемой службой удостоверений для ресурса *кластера* .

> [!IMPORTANT]
> Скопируйте и сохраним значение "Principal-ID", так как оно понадобится в следующих шагах.


### <a name="grant-key-vault-permissions"></a>предоставление Key Vault разрешений

> [!IMPORTANT]
> Этот шаг следует выполнить после получения подтверждения от группы продуктов через канал Майкрософт, который был выполнен Azure Monitor подготовки кластера данных (кластер ADX). Обновление политики Key Vaultного доступа до этой подготовки может завершиться ошибкой.

Обновите Key Vault с помощью новой политики доступа, которая предоставляет разрешения для ресурса *кластера* . Эти разрешения используются подсистемой Azure Monitor хранения для шифрования данных.
Откройте Key Vault в портал Azure и щелкните "политики доступа", а затем "+ добавить политику доступа", чтобы создать новую политику с этими параметрами:

- Разрешения ключа: выберите "Get", "обернуть ключ" и "разворачивать ключ".
- Выбор субъекта: введите значение идентификатора участника, которое было возвращено в ответе на предыдущем шаге.

![предоставление Key Vault разрешений](media/customer-managed-keys/grant-key-vault-permissions.png)

Разрешение *Get* необходимо для проверки того, что Key Vault настроена как восстанавливаемая для защиты ключа и доступа к данным Azure Monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Обновление ресурса кластера с подробными сведениями об идентификаторе ключа

Этот шаг применяется к будущим обновлениям версии ключа в Key Vault. Обновите ресурс *кластера* с key Vault сведениями об *идентификаторе ключа* , чтобы разрешить хранилищу Azure Monitor использовать новую версию ключа. Выберите текущую версию ключа в Azure Key Vault, чтобы получить сведения об идентификаторе ключа.

![предоставление Key Vault разрешений](media/customer-managed-keys/key-identifier-8bit.png)

Обновите ресурс *кластера* кэйваултпропертиес, используя сведения об идентификаторе ключа.

**Update**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>",
   "identity": { 
     "type": "systemAssigned" 
     }
}
```
"Кэйваултпропертиес" содержит сведения об идентификаторе ключа Key Vault.

**Ответ**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
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

> [!NOTE]
> Этот шаг следует выполнить **только** после получения подтверждения от группы продуктов по каналу Майкрософт, который **Azure Monitor подготовки хранилища данных (кластера ADX)** . При связывании рабочих областей и приема данных до этой **подготовки**данные будут удалены и не будут восстановлены.

Для Application Insights конфигурации CMK Используйте содержимое приложения для этого шага.

Для выполнения этой операции необходимо иметь разрешения "запись" как в рабочей области, так и в ресурс *кластера* . это действие включает следующие действия:

- Рабочая область: Microsoft. OperationalInsights/рабочие области/запись
- В ресурсе *кластера* : Microsoft. OperationalInsights/Clusters/Write

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Ответ**

```json
{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

После сопоставления данные, отправляемые в ваши рабочие области, сохраняются в зашифрованном виде с помощью управляемого ключа.

### <a name="workspace-association-verification"></a>Проверка взаимосвязей рабочих областей
Вы можете проверить, связана ли Рабочая область с ресурсом *кластера* , просмотрев [рабочую область — получить](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) ответ. Связанная Рабочая область будет иметь свойство "Клустерресаурцеид" с ИДЕНТИФИКАТОРом ресурса *кластера* .

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
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
      "enableLogAccessUsingOnlyResourcePermissions": true/false,
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

Azure Monitor хранилище всегда будет учитывать изменения ключевых разрешений в течение часа, обычно быстрее, и хранилище станет недоступным. Все данные, принимаемые в рабочие области, связанные с ресурсом *кластера* , удаляются, и запросы завершатся ошибкой. Ранее полученные данные остаются недоступными в хранилище Azure Monitor при условии, что ваш ключ отозван, и ваши рабочие области не удаляются. Недоступные данные определяются политикой хранения данных и удаляются при достижении срока хранения.

Хранилище периодически опрашивает Key Vault, чтобы попытаться расшифровать ключ шифрования и получить доступ к нему, а затем возобновить прием данных и выполнить запрос в течение 30 минут.

## <a name="cmk-kek-rotation"></a>Вращение CMK (KEK)

Для вращения CMK требуется явное обновление ресурса *кластера* с новой версией ключа Azure Key Vault. Чтобы обновить Azure Monitor с помощью новой версии ключа, следуйте инструкциям в разделе "Обновление ресурса *кластера* с подробными сведениями об *идентификаторе ключа* ".

Если обновить ключ в Key Vault и не обновить сведения о новом *идентификаторе ключа* в ресурсе *кластера* *, Azure Monitor хранилище будет использовать предыдущий ключ.

## <a name="limitations-and-constraints"></a>Ограничения и ограничения

- Функция CMK поддерживается на уровне кластера ADX и требует выделенного кластера Azure Monitor ADX

- Максимальное число ресурсов *кластера* на подписку ограничено 5

- Связь ресурсов *кластера* с рабочей областью должна быть перенесена только после получения подтверждения от группы продуктов, которую выполняла подготовка кластера ADX. Данные, отправляемые до этой подготовки, будут удалены и не будут восстановлены.

- Шифрование CMK применяется к вновь принимаемым данным после настройки CMK. Данные, которые были приняты до настройки CMK, оставались зашифрованными с помощью ключа Microsoft Key. Вы можете запрашивать данные до и после настройки без проблем.

- После того как Рабочая область связана с ресурсом *кластера* , она не может быть отменена из ресурса *кластера* , так как данные шифруются с помощью ключа и недоступны без KEK в Azure Key Vault.

- Azure Key Vault должны быть настроены как восстанавливаемые. Эти свойства не включены по умолчанию и должны быть настроены с помощью CLI и PowerShell:

  - Необходимо включить [обратимое удаление](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - Чтобы защититься от принудительного удаления секрета или хранилища даже после обратимого удаления, необходимо включить [защиту](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) от удаления.

- Для Application Insights и Log Analytics требуются отдельные ресурсы *кластера* . Тип ресурса *кластера* определяется во время создания, присвоив свойству "clusterType" значение "LogAnalytics" или "ApplicationInsights". Невозможно изменить тип ресурса *кластера* .

- Перемещение ресурса *кластера* в другую группу ресурсов или подписку сейчас не поддерживается.

- Ваша Azure Key Vault, *кластерный* ресурс и связанные рабочие области должны находиться в одном и том же регионе и в одном клиенте Azure Active Directory (Azure AD), но они могут находиться в разных подписках.

- Связь рабочей области с ресурсом *кластера* завершится ошибкой, если она связана с другим ресурсом *кластера* .

## <a name="troubleshooting-and-management"></a>Устранение неполадок и управление

- Доступность Key Vault
    - В нормальной работе кэши хранилища АЕК в течение короткого периода времени, чтобы периодически возвращаться к Key Vault для распаковки.
    
    - Ошибки временной связи. Хранилище обрабатывает временные ошибки (время ожидания, сбои подключений, проблемы DNS), позволяя ключам оставаться в кэше в течение короткого промежутка времени, а это приводит к небольшому нестабильной работеу доступности. Возможности запросов и приема продолжаются без перерывов.
    
    - Активный сайт, недоступность около 30 минут приведет к тому, что учетная запись хранения станет недоступной. Возможность запросов недоступна, и полученные данные кэшируются в течение нескольких часов с помощью ключа Майкрософт, чтобы избежать потери данных. При восстановлении доступа к Key Vault запрос становится доступным, а временные кэшированные данные принимаются в хранилище данных и шифруются с помощью CMK.

- Если вы создадите ресурс *кластера* и сразу укажете кэйваултпропертиес, операция может завершиться ошибкой, так как политика доступа не может быть определена, пока не будет назначено системное удостоверение ресурсу *кластера* .

- Если вы обновляете существующий ресурс *кластера* с помощью кэйваултпропертиес и в Key Vault отсутствует политика доступа к ключам "Get", операция завершится ошибкой.

- При попытке удалить ресурс *кластера* , связанный с рабочей областью, операция удаления завершится ошибкой.

- Используйте этот API для получения всех ресурсов *кластера* для группы ресурсов:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
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

- Используйте этот вызов API, чтобы получить все ресурсы *кластера* для подписки:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Ответ**
    
  Тот же ответ, что и для "ресурсов*кластера* для группы ресурсов", но в области подписки.
    
- Используйте этот вызов API для удаления ресурса *кластера* . перед удалением ресурса *кластера* необходимо удалить все связанные рабочие области:

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Ответ**

  200 ОК


## <a name="appendix"></a>Приложение

Application Insights также поддерживается управляемый ключ клиента (CMK), однако следует учесть следующее изменение, которое поможет спланировать развертывание CMK для компонентов Application Insight.

Log Analytics и Application Insights используют одну и ту же платформу хранилища данных и обработчик запросов. Мы объединяем эти два хранилища вместе с помощью интеграции Application Insights в Log Analytics, чтобы предоставить единое хранилище журналов в Azure Monitor за второй квартал
2020. Это изменение приведет к внесению данных Application Insights в Log Analytics рабочие области и сделать запросы, аналитические данные и другие улучшения, которые могут быть доступны в процессе настройки CMK в рабочей области, также будут применяться к данным Application Insights.

> [!NOTE]
> Если вам не нужно развертывать CMK для данных Application Insights до интеграции, рекомендуется подождать с Application Insights CMK, так как такие развертывания будут нарушены интеграцией, и потребуется повторно настроить CMK после миграции для записи в журнал. Рабочая область "аналитика". Минимальное значение 1 ТБ в день применяется на уровне кластера и до завершения консолидации в течение второго квартала Application Insights и Log Analytics требуются отдельные кластеры.

## <a name="application-insights-cmk-configuration"></a>Конфигурация Application Insights CMK

Конфигурация Application Insights CMK идентична процессу, показанному в этой статье, включая ограничения и устранение неполадок, за исключением следующих шагов:

- Создание ресурса *кластера*
- Связывание компонента с ресурсом *кластера*

При настройке CMK для Application Insights используйте следующие действия вместо перечисленных выше.

### <a name="create-a-cluster-resource"></a>Создание ресурса *кластера*

Этот ресурс используется в качестве промежуточного подключения между Key Vault и компонентами. После получения подтверждения о том, что ваши подписки были список разрешений, создайте ресурс *кластера* log Analytics в регионе, где находятся компоненты. Тип ресурса *кластера* определяется во время создания, присвоив свойству *clusterType* значение *LogAnalytics*или *ApplicationInsights*. Он должен быть *ApplicationInsights* для Application Insights CMK. Параметр *clusterType* не может быть изменен после настройки.

**Создание**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**Ответ**

Удостоверение назначается ресурсу *кластера* во время создания.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id" 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
"идентификатор участника" — это идентификатор GUID, созданный управляемой службой удостоверений.

> [!IMPORTANT]
> Скопируйте и сохраним значение "принципе-ID", так как оно понадобится в следующих шагах.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Связывание компонента с ресурсом *кластера* с помощью [компонентов — создание или обновление](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

Для выполнения этой операции необходимо иметь разрешения на запись как для компонента, так и для ресурса *кластера* , в том числе следующие действия:

- В компоненте: Microsoft. Insights/компонент/запись
- В ресурсе *кластера* : Microsoft. OperationalInsights/Clusters/Write

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
