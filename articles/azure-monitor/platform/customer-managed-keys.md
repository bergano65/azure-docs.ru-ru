---
title: Конфигурация ключей с управлением клиентов Azure Monitor
description: Информация и шаги по настройке ключа, управляемого клиентом (CMK) для шифрования данных в рабочих областях анализа журналов с помощью ключа Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 03/26/2020
ms.openlocfilehash: 563a50d4589a83f710caa8ff2d2d95065909fc5f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384183"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Конфигурация ключей с управлением клиентов Azure Monitor 

В этой статье содержится справочная информация и шаги для настройки ключей, управляемых клиентами (CMK) для рабочих областей анализа журналов и компонентов Application Insights. После настройки любые данные, отправленные в рабочие области или компоненты, шифруются ключом Azure Key Vault.

Мы рекомендуем вам просмотреть [ограничения и ограничения](#limitations-and-constraints) ниже перед конфигурацией.

## <a name="disclaimers"></a>Заявления об отказе от ответственности

- Azure Monitor CMK — это функция раннего доступа, включенная для зарегистрированных подписок.

- Развертывание CMK, описанное в этой статье, поставляется в качестве продукции и поддерживается как таковое, хотя это функция раннего доступа.

- Возможность CMK поставляется на специальном кластере хранения данных, который является кластером Azure Data Explorer (ADX) и подходит для клиентов, отправляющих 1 ТБ в день или более. 

- Модель ценообразования CMK в настоящее время недоступна и не описана в данной статье. Модель ценообразования для выделенного кластера ADX, как ожидается, во втором квартале календарного года (CY) 2020 и будет применяться к любым существующим развертываниям CMK.

- В этой статье описывается конфигурация CMK для рабочих областей анализа журналов. Компоненты CMK для application Insights также поддерживаются с помощью этой статьи, в то время как различия перечислены в приложении.

> [!NOTE]
> Аналитика журналов и анализ приложений использует одну и ту же платформу хранения данных и движок запросов.
> Мы объединяем эти два магазина через интеграцию Application Insights в Log Analytics для создания единого единого магазина журналов под Azure Monitor. Это изменение запланировано на второй квартал 2020 календарного года. Если к тому времени вам не нужно развертывать CMK для данных Application Insights, мы рекомендуем дождаться завершения консолидации, так как такие развертывания будут нарушены консолидацией, и вам придется перенастроить CMK после миграции в журнал Аналитическое рабочее пространство. Минимум 1 ТБ в день применяется на уровне кластера и до завершения консолидации во втором квартале, Application Insights и Log Analytics требуют отдельных кластеров.

## <a name="customer-managed-key-cmk-overview"></a>Обзор ключа, управляемого клиентами (CMK)

[Шифрование в Rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) является общим требованием конфиденциальности и безопасности в организациях. Вы можете позволить Azure полностью управлять шифрованием в Rest, в то время как у вас есть различные варианты для тесного управления ключами шифрования или шифрования.

Хранилище данных Azure Monitor гарантирует, что все данные, зашифрованные в состоянии покоя, с помощью ключей, управляемых Azure, при хранении в Хранилище Azure. Azure Monitor также предоставляет опцию шифрования данных с помощью собственного ключа, который хранится в [Azure Key Vault,](https://docs.microsoft.com/azure/key-vault/key-vault-overview)доступ к которому осуществляется с помощью системной управляемой проверки [подлинности идентификации личности.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Этот ключ может быть [либо программное обеспечение или аппаратное обеспечение HSM защищены.](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
Использование шифрования Azure Monitor идентично тому, как работает [шифрование Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)

Частота доступа Ки-мониторового хранилища к Key Vault для операций по обертыванию и разворачиванию составляет от 6 до 60 секунд.Azure Monitor Storage всегда уважает изменения в ключевых разрешениях в течение часа.

Полученные данные за последние 14 дней также хранятся в горячем кэше (SSD-поддержке) для эффективной работы движка запроса. Эти данные остаются зашифрованными с помощью ключей Майкрософт независимо от конфигурации CMK, но мы работаем над тем, чтобы SSD был зашифрован с помощью CMK в начале 2020 года.

## <a name="how-cmk-works-in-azure-monitor"></a>Как работает CMK в Azure Monitor

Azure Monitor использует назначенные системой управляемые удостоверения, чтобы предоставить доступ к вашему Убежище ключа Azure.Управляемая итоговая личность, назначенная системой, может быть связана только с одним ресурсом Azure. Идентификация хранилища данных Azure Monitor (кластер ADX) поддерживается на уровне кластера, и это диктует, что возможность CMK поставляется на выделенном кластере ADX. Для поддержки CMK на нескольких рабочих пространствах новый ресурс Log Analytics *(кластер)* выполняет сяочную идентификационную связь между вашим ключом Vault и рабочими пространствами Log Analytics. Эта концепция соответствует ограничению идентификации, назначенному системой, и идентификация сохраняется между кластером ADX и ресурсом Log Analytics *Cluster,* в то время как данные всех связанных рабочих областей защищены ключом Key Vault. В блокированном хранилище ADX используется\'управляемая идентификаторная личность, связанная с ресурсом *кластера,* для проверки подлинности и доступа к вашему Хранилище ключей Azure через Azure Active Directory.

![Обзор CMK](media/customer-managed-keys/cmk-overview.png)
1.  Ключевое убежище клиента.
2.  Ресурс *кластера* аналитики журналов клиентов, управляющий идентификацией с разрешениями на Key Vault - идентификация поддерживается на уровне хранения данных (ADX кластера).
3.  Azure Monitor выделил aDX кластер.
4.  Рабочие области клиента, связанные с кластерным ресурсом для шифрования CMK.

## <a name="encryption-keys-management"></a>Управление ключами шифрования

В шифровании данных хранилища участвуют 3 типа:

- **KEK** - Ключ шифрования в ключе Vault (CMK)
- **AEK** - Ключ шифрования учетной записи
- **DEK** - Ключ шифрования данных

Применяются следующие правила.

- Учетная запись хранения ADX генерирует уникальный ключ шифрования для каждой учетной записи хранения, которая известна как AEK.

- AEK используется для получения DEKs, которые являются ключами, которые используются для шифрования каждого блока данных, написанных на диск.

- При настройке ключа в Key Vault и ссылке на него в ресурсе *кластера* хранилище Azure Data отправляет запросы в ваш Хранилище ключей Azure для обертывания и разворачивания AEK для выполнения операций шифрования и расшифровки данных.

- Ваш KEK никогда не покидает ваш ключ Vault и в случае HSM ключ, он никогда не покидает аппаратное обеспечение.

- Azure Storage использует управляемый идентификатор, связанный с ресурсом *кластера,* для проверки подлинности и доступа к Убежище ключаAzне через Active Directory Azure.

## <a name="cmk-provisioning-procedure"></a>Процедура подготовки CMK

Для конфигурации Application Insights CMK следуйте содержимому приложения для шагов 3 и 6.

1. Подписка белый список - это необходимо для этой функции раннего доступа
2. Создание Убежища ключей Azure и хранение ключа
3. Создание *кластерного* ресурса
4. Подготовка хранилища данных Azure Monitor (кластер ADX)
5. Предоставление разрешений на ваше убежище ключа
6. Связывание рабочих областей анализа журналов

Процедура не поддерживается в UI в настоящее время и процесс подготовки выполняется через REST API.

> [!IMPORTANT]
> Любой запрос API должен включать маркер авторизации Bearer в заголовок запроса.

Пример:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Где *eyJ0eXAiO....* 

Приобрести токен можно одним из следующих методов:

1. Используйте метод [регистрации приложений.](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)

2. На портале Azure
    1. Перейдите на портал Azure в "инструмент разработчика (F12)
    1. Ищите строку авторизации в рамках "Запрос заголовки" в одном из экземпляров "batch?api-версия". Оно смотрит как: «авторизация:\>Токен Bearer \<«. 
    1. Копируйте и добавляйте его в свой вызов API в приведенных ниже примерах.

3. Перейдите на сайт документации Azure REST. Нажмите "Попробуйте" на любом API и скопируйте токен Bearer.

### <a name="subscription-whitelisting"></a>Подписка белый список

Возможность CMK — это функция раннего доступа. Подписки, в которых планируется создавать ресурсы *кластера,* должны быть заранее внесены в белый список группой продуктов Azure. Используйте свои контакты в корпорации Майкрософт для предоставления идонов подписок.

> [!IMPORTANT]
> Возможности CMK региональные. Ваш ресурс Azure Key Vault, ресурс *кластера* и связанные с ними рабочие области журналов должны находиться в одном регионе, но они могут быть в разных подписках.

### <a name="storing-encryption-key-kek"></a>Хранение ключа шифрования (KEK)

Создайте или используйте хранилище ключей Azure, которое вам уже необходимо создать, или импортируйте ключ, который будет использоваться для шифрования данных. Хранилище ключей Azure должно быть настроено как восстанавливаемое для защиты ключа и доступа к вашим данным в Azure Monitor. Вы можете проверить эту конфигурацию под свойствами в вашем убежище ключа, как *мягкое удаление* и *очистка защиты* должны быть включены.

Эти настройки доступны через CLI и PowerShell:
- [обратимое удаление](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Очистка охранников](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) от силы удаления тайны / свода даже после мягкого удаления

### <a name="create-cluster-resource"></a>Создание *кластерного* ресурса

Этот ресурс используется в качестве промежуточного идентификационного соединения между вашим Убежищем ключей и рабочими пространствами. После получения подтверждения того, что ваши подписки были включены в белый список, создайте ресурс *кластера* log Analytics в регионе, где расположены рабочие области. Анализ приложений и аналитика журналов требуют отдельных типов ресурсов *кластеров.* Тип ресурса *кластера* определяется во время создания, установив свойство "clusterType" либо на "LogAnalytics", либо "ApplicationInsights". Тип ресурса кластера не может быть изменен после.

Для конфигурации Application Insights CMK следуйте содержимому приложения.

При создании ресурса *кластера* необходимо указать уровень резервирования емкости (sku) для ресурса *кластера.* Уровень резервирования емкости может находиться в диапазоне от 1000 до 2000, и вы можете обновить его в шагах 100 позже. Если вам нужен уровень резервирования емкости выше 2000, обратитесь к своему контакту с корпорацией Майкрософт, чтобы включить его. Это свойство не влияет на выставление счетов в настоящее время - как только модель ценообразования для выделенного кластера будет введена, выставление счетов будет применяться к любым существующим развертываниям CMK.

**Создать**

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
Идентификация назначается ресурсу *кластера* во время создания.

**Ответ**

202 Принято. Это стандартный ответ менеджера ресурсов для асинхронных операций.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Подготовка хранилища данных Azure Monitor (кластер ADX)

В течение раннего периода доступа к функции кластер ADX вручную предоставляется группой разработчиков после завершения предыдущих шагов. Используйте свой канал Майкрософт для подготовки, обеспечивая при этом ответ ресурсов *кластера.* 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Копирование и сохранение ответа, так как вам понадобятся эти сведения в последующих шагах

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

>[!Important]
> Подготовка недостаточного кластера ADX занимает несколько минут. Значение *положения provisioningState* указывает его состояние, оно *ProvisioningAccount* пока provisioning и «успешно» когда представление завершено.
> GUID "principalId" генерируется управляемой службой идентификации ресурса *кластера.*

### <a name="grant-key-vault-permissions"></a>Грант Ключевые Разрешения Убежища

Обновите key Vault новой политики доступа, которая предоставляет разрешения ресурсу *кластера.* Эти разрешения используются подкладным хранилищем Azure Monitor для шифрования данных. Откройте свой ключевой vault в Azure портала и нажмите кнопку "Политики доступа", а затем "Добавить политику доступа", чтобы создать политику с этими настройками:

- Ключевые разрешения: выберите разрешения 'Get', 'Wrap Key' и 'Unwrap Key'.
- Выберите основной принцип: введите значение основного идентификатора, возвращенное в ответ на предыдущем этапе.

![предоставление разрешений Key Vault](media/customer-managed-keys/grant-key-vault-permissions.png)

*Разрешение Get* необходимо для проверки того, что хранилище ключей настроено как восстанавливаемое для защиты ключа и доступа к данным Azure Monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Обновление ресурса кластера с ключевыми деталями идентификатора

Этот шаг применяется в исходных и будущих ключевых версиях в вашем Ключе Vault. Он информирует Azure Monitor Storage о новой ключевой версии.

Чтобы обновить ресурс *кластера* с помощью *идентификатора* Key Vault, выберите текущую версию ключа в Azure Key Vault, чтобы получить сведения об идентификаторе ключей.

![Грант Ключевые Разрешения Убежища](media/customer-managed-keys/key-identifier-8bit.png)

Обновление *кластерного* ресурса KeyVaultProperties с помощью ключевых идентификаторов.

**Обновление**

>[!Warning]
> Вы должны предоставить полный орган в *кластере* обновления ресурсов, который включает в себя *идентичность,* *sku*, *KeyVaultProperties* и *местоположение.* Отсутствующие детали *KeyVaultProperties* удалит идентификатор ключа из *ресурса кластера* и вызовет [отзыв ключей.](#cmk-kek-revocation)

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
"KeyVaultProperties" содержит детали идентификатора ключей Vault.

**Ответ**

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

### <a name="workspace-association-to-cluster-resource"></a>Ассоциация рабочего пространства с *кластерным* ресурсом

Для конфигурации Application Insights CMK следуйте содержимому приложения для этого шага.

> [!IMPORTANT]
> Этот шаг должен быть выполнен только после подготовки кластера ADX. Если вы связываете рабочие области и глотать данные до подготовки, пропаженные данные будут удалены и не будут восстановлены.
> Чтобы убедиться, что кластер ADX подготовлен, выполните ресурс *кластера* Получите REST API и проверьте, что значение *положения подготовки* *выполняется*успешно.

Для выполнения этой операции необходимо иметь «написать» разрешения как для рабочего пространства, так и для ресурса *кластера,* которые включают следующие действия:

- В рабочей области: Microsoft.OperationalInsights/workspaces/write
- В *кластерном* ресурсе: Microsoft.OperationalInsights/clusters/write

**Ассоциировать рабочее пространство**
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

После объединения рабочих пространств данные, попаданные в рабочие области, хранятся в зашифрованном виде с помощью управляемого ключа.

### <a name="workspace-association-verification"></a>Проверка ассоциации рабочего пространства
Вы можете проверить, связано ли рабочее пространство с ресурсом *Custer,* взглянув на [рабочие области — Получить](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) ответ. Связанное рабочее пространство будет иметь свойство clusterResourceId с идентификатором ресурса *кластера.*

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

Вы можете отозвать доступ к данным, отключив ключ или удаляя политику доступа к ресурсам *кластера* в вашем ключе Vault. Хранилище Azure Monitor всегда будет уважать изменения в ключевых разрешениях в течение часа, как правило, раньше, и хранилище станет недоступным. Любые данные, попадание в рабочие области, связанные с ресурсом *кластера,* отогнаны, и запросы сбой. Ранее попадаенные данные остаются недоступными в хранилище Azure Monitor при покобничеству ключа и удалении рабочих пространств. Недоступные данные регулируются политикой хранения данных и будут удалены по времени хранения данных.

Хранение будет периодически опрашивать ваш ключ Vault, чтобы попытаться развернуть ключ шифрования и после доступа, воспроизглашение данных и возобновление запроса в течение 30 минут.

## <a name="cmk-kek-rotation"></a>Ротация CmK (KEK)

Вращение CMK требует явного обновления ресурса *кластера* с новой ключевой версией в Azure Key Vault. Чтобы обновить Azure Monitor с новой ключевой версией, следуйте инструкциям в шаге *«Обновление* кластерного ресурса с деталями ключевых идентификаторов». Если вы обновите свою ключевую версию в Key Vault и не будете обновлять новые сведения об идентификаторах ключей в ресурсе *кластера,* хранилище Azure Monitor будет продолжать использовать ваш предыдущий ключ.
Все ваши данные доступны после операции вращения ключей, включая данные, попаренные до вращения и после него, так как все данные остаются зашифрованными ключом шифрования учетной записи (AEK), в то время как теперь они зашифрованы новой версией Key Encryption Key (KEK).

## <a name="limitations-and-constraints"></a>Ограничения и ограничения

- Функция CMK поддерживается на уровне кластера ADX и требует выделенного кластера Azure Monitor ADX

- Максимальное количество ресурсов *кластера* за подписку ограничено 2

- *Ассоциация* кластерных ресурсов в рабочее пространство должна быть перенесена только после того, как вы получили подтверждение от группы продуктов о том, что подготовка кластера ADX была выполнена. Данные, отправляемые до этого подготовки, будут удалены и не будут восстановлены.

- Шифрование CMK применяется к вновь пропающим данным после конфигурации CMK. Данные, которые были проглочены до конфигурации CMK, остаются зашифрованными с помощью ключа Майкрософт. Вы можете загонять данные до и после конфигурации CMK бесшовно.

- После того, как рабочее пространство связано с ресурсом *кластера,* оно не может быть отделено от ресурса *кластера,* так как данные зашифрованы ключом и недоступны без вашего KEK в Хранилище ключей Azure.

- Хранилище ключей Azure должно быть настроено как восстанавливаемое. Эти свойства не включены по умолчанию и должны быть настроены с помощью CLI и PowerShell:

  - [Мягкое Удаление](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) должно быть включено
  - [Защита очистки](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) должна быть включена, чтобы защититься от снятия силы секрет / хранилище даже после мягкого удаления

- Анализ приложений и аналитика журналов требуют отдельных ресурсов *кластера.* Тип ресурса *кластера* определяется во время создания, установив свойство "clusterType" либо на 'LogAnalytics', либо 'ApplicationInsights'. Тип ресурса *кластера* не может быть изменен.

- *Перемещение кластерного* ресурса в другую группу ресурсов или подписку в настоящее время не поддерживается.

- Ресурс Azure Key Vault, *ресурс кластера* и связанные с ними рабочие области должны находиться в одном регионе и в том же клиенте Azure Active Directory (Azure AD), но они могут быть в разных подписках.

- Ассоциация рабочего пространства с ресурсом *кластера* выйдет из строя, если она связана с другим ресурсом *кластера*

## <a name="troubleshooting-and-management"></a>Устранение неполадок и управление

- Доступность Ключ убежища
    - При нормальной работе тайники хранилища AEK в течение коротких периодов времени периодически возвращаются в Key Vault, чтобы развернуться.
    
    - Переходные ошибки соединения. Хранение обрабатывает временные ошибки (тайм-ауты, сбои в подключении, проблемы с DNS), позволяя ключам оставаться в кэше на короткое время дольше, и это преодолевает любые небольшие всплески в доступности. Возможности запроса и приема не продуваем продолжаются без перерыва.
    
    - Live сайт, недоступность около 30 минут приведет к учетной записи хранения, чтобы стать недоступным. Возможность запроса недоступна, и данные, попавшиее в проем, кэшируются в течение нескольких часов с помощью ключа Майкрософт, чтобы избежать потери данных. Когда доступ к Key Vault будет восстановлен, запрос становится доступным, а временные кэшированные данные посягают в хранилище данных и шифруются CMK.

- При немедленном создании ресурса *кластера* и указании KeyVaultProperties операция может выйти из строя, так как политика доступа не может быть определена до тех пор, пока ресурс *кластера* не будет назначен системным удостоверением.

- При обновлении существующего ресурса *кластера* с помощью KeyVaultProperties и политики доступа к ключу в Key Vault отсутствует политика доступа к ключу, операция выйдет из строя.

- Если вы попытаетесь удалить ресурс *кластера,* связанный с рабочим пространством, операция удаления выйдет из строя.

- Получите все ресурсы *кластера* для группы ресурсов:

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

- Получите все ресурсы *кластера* для подписки:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Ответ**
    
  Тот же ответ, что и для *"Кластерных* ресурсов для группы ресурсов", но в области подписки.
    
- Удаление ресурса *кластера* - выполняется операция "мягкого удаления", позволяющая восстановить ресурс *кластера,* данные и связанные с ними рабочие области в течение 14 дней, независимо от того, было ли удаление случайным или преднамеренным. После периода «мягкого *удаления»* ресурс кластера и данные не могут быть восстановлены. Имя ресурса *кластера* остается зарезервированным в период смягче-удаляемого, и вы не можете создать новый кластер с этим именем.

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Ответ**

  200 ОК

- Восстановление ресурса *кластера* и данных — в период смягче-удаляния создайте ресурс *кластера* с тем же именем и в той же подписке, группе ресурсов и регионе. Следуйте шагу **ресурса «Создание *кластера»,* ** чтобы восстановить ресурс *кластера.*


## <a name="appendix"></a>Приложение

Поддерживается также управляемый ключ клиента Application Insights (CMK), хотя следует рассмотреть следующие изменения, которые помогут вам спланировать развертывание CMK для компонентов Application Insight.

Аналитика журналов и анализ приложений использует одну и ту же платформу хранения данных и движок запросов. Мы объединяем эти два магазина через интеграцию Application Insights в Log Analytics, чтобы обеспечить единый единый магазин журналов под Azure Monitor ко второму кварталу
2020. Это изменение приведет данные Application Insight в рабочие области Log Analytics и сделает возможными запросы, сведения и другие улучшения, в то время как конфигурация CMK в вашем рабочем пространстве будет также применяться к данным Application Insights.

> [!NOTE]
> Если вам не нужно развертывать CMK для данных Application Insight до интеграции, мы рекомендуем подождать с Application Insights CMK, так как такие развертывания будут нарушены интеграцией, и вам придется перенастроить CMK после миграции в журнал Аналитическое рабочее пространство. Минимум 1 ТБ в день применяется на уровне кластера и до завершения консолидации во втором квартале, Application Insights и Log Analytics требуют отдельных кластеров.

## <a name="application-insights-cmk-configuration"></a>Конфигурация Application Insights CMK

Конфигурация Application Insights CMK идентична процессу, иллюстрированному в этой статье, включая ограничения и устранение неполадок, за исключением следующих шагов:

- Создание *кластерного* ресурса
- Связать компонент с ресурсом *кластера*

При настройке CMK для application Insights используйте эти шаги вместо перечисленных выше.

### <a name="create-a-cluster-resource"></a>Создание *кластерного* ресурса

Этот ресурс используется в качестве промежуточного идентификационного соединения между вашим Убежищем ключей и компонентами. После того, как вы получили подтверждение того, что ваши подписки были в белом списке, создайте ресурс *кластера* log Analytics в регионе, где находятся компоненты. Тип ресурса *кластера* определяется во время создания, установив свойство *clusterType* либо *на LogAnalytics,* либо *на ApplicationInsight.* Это должно быть *ApplicationInsights* для приложений Исследования CMK. Настройка *clusterType* не может быть изменена после конфигурации.

**Создать**

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

Идентификация присваивается ресурсу *кластера* во время создания.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
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
"принцип-id" - это GUID, который был создан управляемой службой идентификации.

> [!IMPORTANT]
> Копировать и сохранять значение "принцип-id", так как оно вам понадобится в следующих шагах.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Связать компонент с ресурсом *кластера* с помощью [компонентов - Создание или обновление](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

Для выполнения этой операции необходимо иметь «записные» разрешения как на компоненте, так и на *ресурсе кластера,* которые включают следующие действия:

- В компоненте: Microsoft.Insights/компонент/запись
- В *кластерном* ресурсе: Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Этот шаг должен быть выполнен только после подготовки кластера ADX. Если вы связываете компоненты и данные до подготовки, пропавшие данные будут удалены и не будут восстановлены.
> Чтобы убедиться, что кластер ADX подготовлен, выполните ресурс *кластера* Получите REST API и проверьте, что значение *положения подготовки* *выполняется*успешно.

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

**Ассоциировать компонент**

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
"clusterDefinitionId" - это значение clusterId, представленное в ответе на предыдущем этапе.
"добрый" пример "веб".

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
"clusterDefinitionId" — это идентификатор ресурсов *кластера,* связанный с этим компонентом.

После объединения данные, отправляемые вашим компонентам, хранятся в зашифрованном виде с помощью управляемого ключа.
