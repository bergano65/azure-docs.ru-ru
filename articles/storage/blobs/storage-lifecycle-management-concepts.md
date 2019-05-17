---
title: Управление жизненным циклом службы хранилища Azure
description: Узнайте, как создать правила для политики жизненного цикла, чтобы перевести данные из горячего уровня доступа на холодный и архивный.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: common
ms.openlocfilehash: 26ff592ea0d0a57049ae11a981fe8d8e77ca876f
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606946"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Управление жизненным циклом хранилища BLOB-объектов Azure

Для каждого набора данных существуют уникальные требования к жизненному циклу. На ранних этапах жизненного цикла данных обращение к ним происходит часто. Но по мере устаревания данных потребность в доступе снижается очень быстро. Некоторые данные хранятся в облаке почти без использования, и после сохранения к ним обращаются крайне редко. Некоторые данные устаревают через несколько дней или месяцев после их создания, а другие наборы данных активно считываются и изменяются на протяжении всего времени существования. Управление жизненным циклом хранилища Azure BLOB-объектов предлагает широкие возможности, основанные на правилах политики для учетных записей хранения GPv2 и больших двоичных объектов. Эти политики позволяют переводить данные на новые уровни доступа и удалять их по завершении жизненного цикла.

Политика управления жизненным циклом поддерживает следующие возможности.

- Перемещение больших двоичных объектов на более холодный уровень доступа (с горячего на холодный, с горячего на архивный, с холодного на архивный) для оптимального баланса производительности и стоимости.
- Удаление больших двоичных объектов в конце их жизненных циклов.
- Определение правил, выполняемых раз в день, на уровне учетной записи хранения.
- Применение правил к контейнерам или подмножествам больших двоичных объектов (с префиксами в качестве фильтров).

Рассмотрим ситуацию, где данные начинают частый доступ на ранних этапах жизненного цикла, но только время от времени по истечении двух недель. Данные, которые хранятся больше месяца, запрашиваются крайне редко. В этом сценарии для ранних этапов лучше всего выбрать горячий уровень хранилища. "Холодное" хранилище лучше всего подходит для время от времени доступа. Архивное хранилище является наилучшим вариантом уровня после их устаревания за месяц. Выбирая уровни хранилища в зависимости от возраста данных, вы сможете создать наиболее экономичный вариант хранилища для конкретных потребностей. Чтобы достичь перемещения данных на более холодные уровни, можно использовать правила политики управления жизненным циклом.

## <a name="storage-account-support"></a>Поддержка учетных записей хранения

Политика управления жизненным циклом поддерживает учетные записи GPv2 (общего назначения, версия 2) и учетные записи хранилища BLOB-объектов. На портале Azure вы можете обновить существующую учетную запись общего назначения (GPv1) для учетной записи GPv2. Дополнительные сведения об учетных записях хранения см. в статье [Общие сведения об учетной записи хранения](../common/storage-account-overview.md).  

## <a name="pricing"></a>Цены

Компонент управления жизненным циклом предоставляется бесплатно. Клиенты оплачивают только обычную стоимость вызовов API [Отображение BLOB-объектов](https://docs.microsoft.com/rest/api/storageservices/list-blobs) и [Установка уровня BLOB-объектов](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier). Операция удаления предоставляется бесплатно. Дополнительные сведения см. на странице [цен на блочные BLOB-объекты](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Доступность в регионах

Компонент управления жизненным циклом доступна во всех глобальных регионах Azure.

## <a name="add-or-remove-a-policy"></a>Добавление или удаление политики

Можно добавить, изменить или удалить политику с помощью любого из следующих методов:

* [портал Azure](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Интерфейсы REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

В этой статье показано, как управлять политикой с помощью портала и методы PowerShell.  

> [!NOTE]
> Если вы настроили для учетной записи хранения правила брандмауэра, запросы на управление жизненным циклом могут быть заблокированы. Их можно разблокировать, настроив исключения. Требуется обход проверки являются: `Logging,  Metrics,  AzureServices`. Дополнительные сведения см. в разделе об исключениях в статье [Настройка брандмауэров службы хранилища Azure и виртуальных сетей](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Портал Azure

1. Войдите на [портале Azure](https://portal.azure.com).

2. Выберите **все ресурсы** и выберите свою учетную запись хранения.

3. В разделе **службы BLOB-объектов**выберите **управление жизненным циклом** для просмотра или изменения политики.

4. Приведенный ниже код JSON является примером правило, которое может быть вставлено в **управление жизненным циклом** страницы портала.

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. Дополнительные сведения об этом примере JSON см. в разделе [политики](#policy) и [правила](#rules) разделы.

### <a name="powershell"></a>PowerShell

Чтобы добавить политику для своей учетной записи хранения можно использовать следующий сценарий PowerShell. `$rgname` Необходимо инициализировать переменную с именем группы ресурсов. `$accountName` Переменная должна быть инициализирована именем учетной записи хранения.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

## <a name="azure-resource-manager-template-with-lifecycle-management-policy"></a>Шаблон Azure Resource Manager с помощью политики управления жизненным циклом

Управление жизненным циклом можно определить с помощью шаблонов Azure Resource Manager. Ниже приведен пример шаблона для развертывания учетной записи хранения GPv2 RA-GRS с политикой управления жизненным циклом.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

## <a name="policy"></a>Политика

Политика управления жизненным циклом представляет собой набор правил, оформленный в виде документа JSON.

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Политика — это коллекция правил:

| Имя параметра | Тип параметра | Примечания |
|----------------|----------------|-------|
| `rules`        | Массив объектов правил | Требуется по крайней мере одно правило в политике. Можно определить до 100 правил в политике.|

Каждое правило в рамках политики есть несколько параметров:

| Имя параметра | Тип параметра | Примечания | Обязательно для заполнения |
|----------------|----------------|-------|----------|
| `name`         | Строка |Имя правила может содержать до 256 буквенно-цифровые символы. В именах правил учитывается регистр.  Имя должно быть уникальным в пределах политики. | Истина |
| `enabled`      | Boolean | Необязательное логическое значение, разрешающее правило для временного отключена. Значение по умолчанию имеет значение true, если оно не задано. | False | 
| `type`         | Значение перечисления | Текущий тип допустимым является `Lifecycle`. | Истина |
| `definition`   | Объект, который определяет правило жизненного цикла | Каждое определение состоит из набора фильтров и набора действий. | Истина |

## <a name="rules"></a>Правила

Каждое определение правила состоит из набора фильтров и набора действий. [Набор фильтров](#rule-filters) ограничивает действие правила определенным набором объектов в контейнере или имен объектов. [Набор операций](#rule-actions) вводит в действие уровень или удаляет действия в отфильтрованном наборе объектов.

### <a name="sample-rule"></a>Пример правила

Следующий пример правила фильтрует учетной записи для выполнения действия с объектами, которые существуют в `container1` и начинаться с `foo`.  

- установить для BLOB-объекта холодный уровень доступа через 30 дней после последнего изменения;
- установить для BLOB-объекта архивный уровень доступа через 90 дней после последнего изменения;
- удалить BLOB-объект спустя 2555 дней (7 лет) после последнего изменения;
- удалить моментальный снимок BLOB-объекта через 90 дней после создания моментального снимка.

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Фильтры правила

Фильтры ограничивают действие правила определенным подмножеством BLOB-объектов в учетной записи хранения. Если определено несколько фильтров, для всех фильтров применяется логическая операция `AND`.

Доступны следующие фильтры:

| Имя фильтра | Тип фильтра | Примечания | Обязательно |
|-------------|-------------|-------|-------------|
| blobTypes   | Массив предустановленных значений перечисления. | В текущем выпуске поддерживается `blockBlob`. | Да |
| prefixMatch | Массив строк, по которым выполняется сопоставление префиксов. Каждое правило можно определить до 10 префиксы. Строка префикса должно начинаться с имени контейнера. Например, если вы хотите обеспечить поиск всех больших двоичных объектов в `https://myaccount.blob.core.windows.net/container1/foo/...` для правила, — prefixMatch `container1/foo`. | Если вы не определили prefixMatch, правило применяется для всех больших двоичных объектов в учетной записи хранения.  | Нет |

### <a name="rule-actions"></a>Действия правила

Действия применяются к фильтрованных больших двоичных объектов, когда выполняется условие выполнения.

Управление жизненным циклом поддерживает распределение по уровням и удаление больших двоичных объектов и удаление моментальных снимков больших двоичных объектов. Определите в каждом правиле хотя бы одно действие для BLOB-объектов или моментальных снимков BLOB-объектов.

| Действие        | Базовый BLOB-объект                                   | Снимок      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Поддержка BLOB-объектов, размещенных на горячем уровне доступа         | Не поддерживается |
| tierToArchive | Поддержка BLOB-объектов, размещенных на горячем или холодном уровне доступа | Не поддерживается |
| удалить        | Поддерживаются                                   | Поддерживаются     |

>[!NOTE]
>Если для одного BLOB-объекта определено более одного действия, управление жизненным циклом применяет к нему более дешевое из этих действий. Например, действие `delete` дешевле, чем действие `tierToArchive`; а действие `tierToArchive` дешевле, чем действие `tierToCool`.

Срок действия зависит от выполнения условий. Возраст для базового BLOB-объекта определяется по времени последнего изменения, а для моментального снимка BLOB-объекта — по времени создания.

| Запуск условие действия             | Значение условия                          | Описание                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Целочисленное значение, указывающее возраст в днях | Условие для действия базового большого двоичного объекта     |
| daysAfterCreationGreaterThan     | Целочисленное значение, указывающее возраст в днях | Условие для действия моментального снимка BLOB-объектов |

## <a name="examples"></a>Примеры

Следующие примеры демонстрируют несколько типичных сценариев для правил политики жизненного цикла.

### <a name="move-aging-data-to-a-cooler-tier"></a>Перемещение старых данных на более холодный уровень

В следующем примере показано перемещение блочных BLOB-объектов с префиксом имени `container1/foo` или `container2/bar`. Эта политика перемещает BLOB-объекты, которые не изменялись более 30 дней, на холодный уровень, а которые не изменялись в течение 90 дней — на архивный уровень:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Архивация данных при поступлении

Некоторые данные хранятся в облаке почти без использования. Следующая политика жизненного цикла настроен для архивации данных, после их приема. В этом примере, переходы блочных BLOB-объектов в учетной записи хранения в контейнере `archivecontainer` в архивный уровень. Переход выполняется путем использования больших двоичных объектов 0 дней после последнего изменения:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Устаревание данных на основе возраста

Ожидается, что некоторые данные истекает дней или месяцев после ее создания. Вы можете настроить политику управления жизненным циклом, чтобы удалять устаревшие данные при достижении определенного возраста. В следующем примере показано политику, которая удаляет все блочных BLOB-объектов больше 365 дней.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Удаление старых моментальных снимков

Для данных, которые часто изменяются и используются на протяжении их существования, часто используются моментальные снимки для отслеживания ранних версий данных. Вы можете создать политику, которая удаляет старые моментальные снимки при достижении определенного возраста. Возраст моментального снимка определяется от момента создания моментального снимка. Это правило политики удаляет моментальные снимки BLOB-объектов в пределах контейнера `activedata`, с момента создания которых прошло 90 или более дней.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Часто задаваемые вопросы

**Я создал новую политику, почему действия не выполняются немедленно?**  
Платформа выполняет политики жизненного цикла один раз в день. После настройки политики, может занять до 24 часов некоторые действия, которые должны выполняться в первый раз.  

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как восстанавливать данные после случайного удаления:

- [Обратимое удаление больших двоичных объектов службы хранилища Azure](../blobs/storage-blob-soft-delete.md)
