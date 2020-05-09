---
title: Непрерывная интеграция и доставка в фабрике данных Azure
description: Сведения об использовании непрерывных интеграции и поставки для перемещения конвейеров Фабрики данных из одной среды (разработки, тестирования, рабочей) в другую.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 87cb7c57aab048e1b7acf211d58c850a41afa5a2
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628245"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Непрерывная интеграция и доставка в фабрике данных Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Обзор

Непрерывная интеграция — это методика автоматического тестирования каждого изменения, внесенного в базу кода, как можно раньше.Непрерывная поставка соответствует тестированию, которое происходит во время непрерывной интеграции и передает изменения в промежуточную или рабочую систему.

В фабрике данных Azure непрерывная интеграция и доставка (CI/CD) означает перемещение конвейеров фабрики данных из одной среды (разработки, тестирования, рабочей) в другую. В фабрике данных Azure используются [шаблоны Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) для хранения конфигурации различных объектов ADF (конвейеры, наборы данных, потоки и т. д.). Существует два предлагаемых метода повышения фабрики данных в другой среде.

-    Автоматическое развертывание с использованием интеграции фабрики данных с [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)
-    Вручную отправьте шаблон диспетчер ресурсов с помощью интеграции с интерфейсом UX фабрики данных с Azure Resource Manager.

Чтобы получить 9-минутное введение в эту функцию и демонстрацию, просмотрите это видео:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Жизненный цикл CI/CD

Ниже приведен примерный обзор жизненного цикла CI/CD в фабрике данных Azure, настроенной с помощью Azure Repos Git. Дополнительные сведения о настройке репозитория Git см. в статье [Управление версиями в фабрике данных Azure](source-control.md).

1.  Фабрика данных разработки создается и настраивается с помощью Azure Repos Git. Все разработчики должны иметь разрешение на создание ресурсов фабрики данных, таких как конвейеры и DataSets.

1.  Разработчик [создает ветвь компонентов](source-control.md#creating-feature-branches) для внесения изменений. Они выполняют отладку своих конвейеров с самыми последними изменениями. Дополнительные сведения об отладке выполнения конвейера см. в статье [Итеративная разработка и отладка с помощью фабрики данных Azure](iterative-development-debugging.md).

1.  После того, как разработчик удовлетворен своими изменениями, он создает запрос на вытягивание из своей ветви компонентов в главную или коллективную ветвь для получения изменений, которые будут проверены одноранговыми узлами.

1.  После утверждения запроса на вытягивание и слияния изменений в главной ветви изменения публикуются в фабрике разработки.

1.  Когда группа готова к развертыванию изменений в тестовой или UAT фабрике, Группа переходит к выпуску Azure Pipelines и развертывает нужную версию фабрики разработки в UAT. Это развертывание выполняется как часть задачи Azure Pipelines и использует параметры шаблона диспетчер ресурсов, чтобы применить соответствующую конфигурацию.

1.  После проверки изменений в фабрике тестов выполните развертывание в производственной фабрике с помощью следующей задачи выпуска конвейеров.

> [!NOTE]
> Только фабрика разработки связана с репозиторием Git. На тестовой и рабочей фабрике не должно быть связанного с ними репозитория Git. их следует обновлять только через конвейер Azure DevOps или с помощью шаблона управления ресурсами.

На приведенном ниже рисунке показаны различные этапы этого жизненного цикла.

![Схема непрерывной интеграции с помощью Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Автоматизация непрерывной интеграции с помощью Azure Pipelines выпусков

Ниже приведено пошаговое инструкции по настройке выпуска Azure Pipelines, который автоматизирует развертывание фабрики данных в нескольких средах.

### <a name="requirements"></a>Требования

-   Подписка Azure, связанная с Team Foundation Server или Azure Repos Visual Studio, использующая [конечную точку службы Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Фабрика данных, настроенная с интеграцией Azure Repos Git.

-    [Хранилище ключей Azure](https://azure.microsoft.com/services/key-vault/) , содержащее секреты для каждой среды.

### <a name="set-up-an-azure-pipelines-release"></a>Настройка выпуска Azure Pipelines

1.  В [Azure DevOps](https://dev.azure.com/)откройте проект, настроенный для фабрики данных.

1.  В левой части страницы выберите **конвейеры**, а затем выберите **выпуски**.

    ![Выбор конвейеров, выпусков](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Выберите **Новый конвейер**или, если имеются существующие конвейеры, выберите **создать** , а затем — **Новый конвейер выпуска**.

1.  Выберите **пустой шаблон задания** .

    ![Выберите пустое задание](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  В поле **имя этапа** введите имя среды.

1.  Выберите **Добавить артефакт**, а затем выберите репозиторий Git, настроенный с помощью фабрики данных разработки. Выберите [ветвь опубликовать](source-control.md#configure-publishing-settings) репозитория для **ветви по умолчанию**. По умолчанию эта ветвь публикации имеет `adf_publish`значение. В качестве **версии по умолчанию**выберите **Последняя из ветви по умолчанию**.

    ![Добавление артефакта](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Добавьте задачу развертывания Azure Resource Manager.

    а.  В представлении этап выберите **просмотреть задачи этапа**.

    ![Представление "этап"](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Создайте новое задание. Найдите **Развертывание группы ресурсов Azure**и нажмите кнопку **Добавить**.

    c.  В задаче развертывание выберите подписку, группу ресурсов и расположение для целевой фабрики данных. При необходимости укажите учетные данные.

    d.  В списке **действие** выберите **создать или обновить группу ресурсов**.

    д)  Нажмите кнопку с многоточием (**...**) рядом с полем **шаблон** . Найдите шаблон Azure Resource Manager, созданный в ветви публикации настроенного репозитория Git. Найдите файл `ARMTemplateForFactory.json` в <FactoryName> папке adf_publish ветви.

    е)  Выберите **...** рядом с полем **Параметры шаблона** , чтобы выбрать файл параметров. Найдите файл `ARMTemplateParametersForFactory.json` в <FactoryName> папке adf_publish ветви.

    ж.  Выберите **...** рядом с полем **переопределить параметры шаблона** и введите значения требуемых параметров для целевой фабрики данных. Для учетных данных, полученных из Azure Key Vault, введите имя секрета в двойные кавычки. Например, если имя секрета — cred1, введите **"$ (cred1)"** для этого значения.

    h. Выберите **добавочный** для **режима развертывания**.

    > [!WARNING]
    > Если выбрать параметр **завершить** для **режима развертывания**, существующие ресурсы могут быть удалены, включая все ресурсы в Целевой группе ресурсов, которые не определены в шаблоне диспетчер ресурсов.

    ![Производственное развертывание фабрики данных](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Сохраните конвейер выпуска.

1. Чтобы запустить выпуск, выберите **создать выпуск**. Сведения об автоматизации создания выпусков см. в статье [триггеры выпуска Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/release/triggers?view=azure-devops)

   ![Выбор создания выпуска](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> В сценариях CI/CD тип среды выполнения интеграции (IR) в разных средах должен быть одинаковым. Например, если в среде разработки есть локальная среда IR, то такой же тип IR также должен быть автономным в других средах, таких как тестовая и Рабочая. Аналогично, при совместном использовании сред выполнения интеграции на нескольких этапах необходимо настроить среды выполнения интеграции как связанные автономные размещения во всех средах, таких как разработка, тестирование и производство.

### <a name="get-secrets-from-azure-key-vault"></a>Получение секретов от Azure Key Vault

Если у вас есть секреты для передачи шаблона Azure Resource Manager, рекомендуется использовать Azure Key Vault с Azure Pipelinesным выпуском.

Существует два способа управления секретами:

1.  Добавьте секреты в файл параметров. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](../azure-resource-manager/templates/key-vault-parameter.md).

    Создайте копию файла параметров, который отправляется в ветвь Publish. Задайте значения параметров, которые требуется получить из Key Vault, в следующем формате:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    При использовании этого метода секрет автоматически извлекается из хранилища ключей.

    Файл параметров также должен находиться в ветви публикации.

1. Добавьте [задачу Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) перед задачей «Azure Resource Manager развертывание», описанной в предыдущем разделе.

    1.  На вкладке **задачи** создайте новую задачу. Выполните поиск **Azure Key Vault** и добавьте его.

    1.  В задаче Key Vault выберите подписку, в которой было создано хранилище ключей. При необходимости введите учетные данные, а затем выберите хранилище ключей.

    ![Добавление задачи Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Предоставление разрешений для агента Azure Pipelines

Azure Key Vaultная задача может завершиться ошибкой с ошибкой отказа в доступе, если не заданы правильные разрешения. Скачайте журналы для выпуска и выберите файл PS1, содержащий команду, чтобы предоставить разрешения агенту Azure Pipelines. Вы можете выполнить команду напрямую. Также можно скопировать идентификатор субъекта из файла и добавить политику доступа вручную в портал Azure. `Get`и `List` являются минимальными необходимыми разрешениями.

### <a name="updating-active-triggers"></a>Обновление активных триггеров

При попытке обновления активных триггеров развертывание может завершиться сбоем. Чтобы обновить активные триггеры, необходимо вручную закрыть их, а затем перезапустить после развертывания. Это можно сделать с помощью задачи Azure PowerShell:

1.  На вкладке **задачи** выпуска добавьте **Azure PowerShell** задачу. Выберите задачу версия 4. *. 

1.  Выберите подписку, в которой находится ваша фабрика.

1.  Выберите **путь к файлу скрипта** в качестве типа скрипта. Для этого необходимо сохранить сценарий PowerShell в репозитории. Для отключения триггеров можно использовать следующий сценарий PowerShell:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Вы можете выполнить аналогичные действия (с `Start-AzDataFactoryV2Trigger` функцией), чтобы перезапустить триггеры после развертывания.

В нижней части этой статьи группа фабрики данных предоставила [Пример сценария, выполняемого до и после развертывания](#script) . 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Ручное повышение диспетчер ресурсов шаблона для каждой среды

1. В списке **шаблон ARM** выберите **Экспорт шаблона ARM** , чтобы экспортировать шаблон диспетчер ресурсов для фабрики данных в среде разработки.

   ![Экспорт шаблона диспетчер ресурсов](media/continuous-integration-deployment/continuous-integration-image1.png)

1. В фабриках тестов и рабочих данных выберите **Импорт шаблона ARM**. После этого вы перейдете на портал Azure, где сможете импортировать экспортированный шаблон. Выберите **создать собственный шаблон в редакторе** , чтобы открыть редактор шаблонов диспетчер ресурсов.

   ![Создание собственного шаблона](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Выберите **загрузить файл**и выберите созданный шаблон диспетчер ресурсов. Это файл **arm_template. JSON** , расположенный в ZIP-файле, экспортированном на шаге 1.

   ![Изменить шаблон](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. В разделе Параметры введите значения конфигурации, например учетные данные связанной службы. Когда все будет готово, выберите **приобрести** , чтобы развернуть шаблон диспетчер ресурсов.

   ![Раздел параметров](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Использование пользовательских параметров с шаблоном Resource Manager

Если у вашей фабрики разработки есть связанный репозиторий Git, можно переопределить параметры шаблона диспетчер ресурсов по умолчанию для шаблона диспетчер ресурсов, созданного путем публикации или экспорта шаблона. Может потребоваться переопределить шаблон параметризации по умолчанию в следующих сценариях:

* Вы используете автоматизированную CI/CD и хотите изменить некоторые свойства во время развертывания диспетчер ресурсов, но свойства не будут параметризованы по умолчанию.
* Ваша фабрика настолько велика, что шаблон диспетчер ресурсов по умолчанию является недопустимым, так как он содержит больше максимально допустимых параметров (256).

Чтобы переопределить шаблон параметризации по умолчанию, создайте файл с именем **ARM-Template-Parameters-Definition. JSON** в корневой папке ветви Git. Необходимо использовать точное имя файла.

   ![Файл пользовательских параметров](media/continuous-integration-deployment/custom-parameters.png)

При публикации из ветви совместной работы фабрика данных считывает этот файл и использует его конфигурацию для создания свойств, которые будут параметризованы. Если файл не найден, используется шаблон по умолчанию.

При экспорте шаблона диспетчер ресурсов фабрика данных считывает этот файл из любой ветви, над которой вы работаете в данный момент, а не только из ветви совместной работы. Вы можете создать или изменить файл из частной ветви, где можно протестировать изменения, выбрав **Экспорт шаблона ARM** в пользовательском интерфейсе. Затем можно выполнить слияние файла в ветвь совместной работы.

> [!NOTE]
> Пользовательский шаблон параметризации не изменяет ограничение на число параметров шаблона ARM, равное 256. Он позволяет выбирать и уменьшать количество параметризованных свойств.

### <a name="custom-parameter-syntax"></a>Пользовательский синтаксис параметров

Ниже приведены некоторые рекомендации, которые следует выполнить при создании файла пользовательских параметров, **ARM-Template-Parameters-Definition. JSON**. Файл состоит из раздела для каждого типа сущности: триггера, конвейера, связанной службы, набора данных, среды выполнения интеграции и потока данных.

* Введите путь к свойству в соответствующем типе сущности.
* Задание имени свойства `*` означает, что необходимо параметризовать все свойства под ним (только на первый уровень, но не рекурсивно). В эту конфигурацию можно также указать исключения.
* Установка значения свойства в виде строки указывает, что необходимо параметризовать свойство. Используйте формат `<action>:<name>:<stype>`.
   *  `<action>` может быть одним из следующих символов:
      * `=` означает, что текущее значение сохраняется как значение по умолчанию для параметра.
      * `-` означает, что значение по умолчанию для параметра не сохраняется.
      * `|` является особым случаем для секретов из Azure Key Vault для строк подключения или ключей.
   * `<name>` имя параметра. Если оно пустое, оно принимает имя свойства. Если значение начинается с `-` символа, оно сокращается. Например, `AzureStorage1_properties_typeProperties_connectionString` сократится до `AzureStorage1_connectionString`.
   * `<stype>` тип параметра. Если `<stype>` параметр пуст, используется тип по умолчанию `string`. Поддерживаемые значения: `string`, `bool`, `number`, `object`и `securestring`.
* Указание массива в файле определения указывает на то, что соответствующее свойство в шаблоне является массивом. Фабрика данных выполняет перебор всех объектов в массиве с помощью определения, указанного в объекте среды выполнения интеграции массива. Второй объект, строка, становится именем свойства, которое используется в качестве имени параметра для каждой итерации.
* Определение не может быть специфичным для экземпляра ресурса. Любое определение применяется ко всем ресурсам этого типа.
* По умолчанию все защищенные строки, такие как Key Vault секреты и защищенные строки, такие как строки подключения, ключи и токены, являются параметризованными.
 
### <a name="sample-parameterization-template"></a>Пример шаблона параметризации

Ниже приведен пример того, как может выглядеть шаблон параметризации:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Ниже приведено объяснение того, как создается предыдущий шаблон, разбитый по типу ресурса.

#### <a name="pipelines"></a>Конвейеры
    
* Любое свойство в пути `activities/typeProperties/waitTimeInSeconds` является параметризованным. Любое действие в конвейере, которое имеет свойство уровня кода с именем `waitTimeInSeconds` (например, `Wait` действие), параметризовано как число с именем по умолчанию. Но в шаблоне диспетчер ресурсов не будет значения по умолчанию. Он будет обязательным входом во время развертывания диспетчер ресурсов.
* Аналогично, свойство с `headers` именем (например, в `Web` действии) параметризовано с типом `object` (JObject). Он имеет значение по умолчанию, то есть то же значение, что и у исходной фабрики.

#### <a name="integrationruntimes"></a>интегратионрунтимес

* Все свойства в пути `typeProperties` параметризованы с соответствующими значениями по умолчанию. Например, в разделе `IntegrationRuntimes` свойства типа есть два свойства: `computeProperties` и. `ssisProperties` Оба типа свойств создаются с соответствующими значениями по умолчанию и типами (Object).

#### <a name="triggers"></a>Триггеры

* В `typeProperties`разделе два свойства являются параметризованными. Первый из них — `maxConcurrency`, для которого задано значение по умолчанию и тип`string`. Он имеет имя `<entityName>_properties_typeProperties_maxConcurrency`параметра по умолчанию.
* `recurrence` Свойство также является параметризованным. В нем все свойства на этом уровне указываются для параметризации в виде строк, значения по умолчанию и имена параметров. Исключением является `interval` свойство, которое параметризовано как тип `number`. Имя параметра имеет суффикс `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Аналогично, `freq` свойство является строкой и параметризовано как строка. Однако `freq` свойство является параметризованным без значения по умолчанию. Имя сокращено и суффиксы. Например, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Связанные службы являются уникальными. Так как связанные службы и наборы данных имеют широкий спектр типов, можно указать настройку для конкретного типа. В этом примере для всех связанных служб типа `AzureDataLakeStore`будет применен конкретный шаблон. Для всех остальных (через `*`) будет применен другой шаблон.
* `connectionString` Свойство будет параметризовано как `securestring` значение. Он не имеет значения по умолчанию. Имя параметра будет сокращено с `connectionString`суффиксом.
* Свойство `secretAccessKey` должно быть равно `AzureKeyVaultSecret` (например, в связанной службе Amazon S3). Он автоматически параметризован как Azure Key Vault секрет и получен из настроенного хранилища ключей. Вы также можете параметризовать само хранилище ключей.

#### <a name="datasets"></a>Наборы данных

* Хотя настройка для наборов данных доступна для конкретного типа, можно предоставить конфигурацию без явной настройки на \*уровне. В предыдущем примере все свойства набора данных в разделе `typeProperties` являются параметризованными.

### <a name="default-parameterization-template"></a>Шаблон параметризации по умолчанию

Ниже приведен текущий шаблон параметризации по умолчанию. Если необходимо добавить только несколько параметров, то изменение этого шаблона напрямую может оказаться хорошей идеей, так как вы не потеряли существующую структуру параметризации.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Пример. Параметризация существующего Azure Databricksного идентификатора интерактивного кластера

В следующем примере показано, как добавить одно значение в шаблон параметризации по умолчанию. Мы хотим добавить существующий Azure Databricks идентификатор интерактивного кластера для связанной службы "кирпичы" в файл параметров. Обратите внимание, что этот файл совпадает с предыдущим файлом, за исключением `existingClusterId` добавления в поле свойства `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Связанные шаблоны Resource Manager

Если вы настроили CI/CD для фабрик данных, вы можете превысить ограничения Azure Resource Manager шаблонов по мере роста фабрики. Например, одним из ограничений является максимальное количество ресурсов в шаблоне диспетчер ресурсов. Для размещения больших фабрик при формировании полного шаблона диспетчер ресурсов фабрика данных теперь создает связанные шаблоны диспетчер ресурсов. С помощью этой функции все полезные данные фабрики разбиваются на несколько файлов, чтобы не ограничиваться ограничениями.

Если вы настроили Git, связанные шаблоны создаются и сохраняются вместе с полными диспетчер ресурсов шаблонами в ветви adf_publish в новой папке с именем Линкедтемплатес:

![Папка связанных шаблонов Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Связанные шаблоны диспетчер ресурсов обычно состоят из главного шаблона и набора дочерних шаблонов, связанных с главным. Родительский шаблон называется ArmTemplate_master. JSON, а дочерние шаблоны именуются шаблоном ArmTemplate_0. JSON, ArmTemplate_1. JSON и т. д. 

Чтобы использовать связанные шаблоны вместо полного шаблона диспетчер ресурсов, обновите задачу CI/CD, чтобы она указывала на ArmTemplate_master. JSON вместо Армтемплатефорфактори. JSON (полный шаблон диспетчер ресурсов). Для диспетчер ресурсов также требуется передать связанные шаблоны в учетную запись хранения, чтобы Azure мог получить к ним доступ во время развертывания. Дополнительные сведения см. в разделе [Развертывание связанных шаблонов диспетчер ресурсов с помощью VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Не забудьте добавить сценарии фабрики данных в конвейер CI/CD до и после завершения развертывания.

Если вы не настроили Git, вы можете получить доступ к связанным шаблонам с помощью **шаблона Export ARM** в списке **шаблонов ARM** .

## <a name="hotfix-production-branch"></a>Рабочая ветвь исправления

Если вы развертываете фабрику в рабочей среде и понимаете, что есть ошибка, которую необходимо исправить немедленно, но вы не можете развернуть текущую ветвь совместной работы, может потребоваться развернуть исправление. Этот подход известен как быстрое устранение неполадок или исправление QFE.

1.    В Azure DevOps перейдите к выпуску, развернутому в рабочей среде. Поиск последней развернутой фиксации.

2.    В сообщении о фиксации получите идентификатор фиксации ветви совместной работы.

3.    Создайте новую ветвь исправления из этой фиксации.

4.    Перейдите в службу "Фабрика данных Azure" и переключитесь на ветвь исправлений.

5.    Исправьте ошибку с помощью UX фабрики данных Azure. Проверьте изменения.

6.    После проверки исправления выберите **Экспорт шаблона ARM** , чтобы получить диспетчер ресурсов шаблона исправления.

7.    Вручную проверить эту сборку в ветви adf_publish.

8.    Если конвейер выпуска настроен на автоматический запуск на основе adf_publish возврата, новый выпуск будет запущен автоматически. В противном случае выпуск выставится в очередь вручную.

9.    Разверните выпуск исправления на фабриках тестирования и производства. Этот выпуск содержит предыдущие рабочие нагрузки, а также исправление, выполненное на шаге 5.

10.   Добавьте изменения из исправления в ветвь Development, чтобы в последующих выпусках не включалась та же ошибка.

## <a name="best-practices-for-cicd"></a>Рекомендации для CI/CD

Если вы используете интеграцию с Git с фабрикой данных и конвейер CI/CD, который перемещает изменения из разработки в тест, а затем в рабочую среду, мы рекомендуем следующие рекомендации:

-   **Интеграция с Git**. Настройка только фабрики данных разработки с помощью интеграции с Git. Изменения в тестовых и рабочих средах развертываются с помощью CI/CD и не требуют интеграции с Git.

-   **Скрипты, выполняемые до и после развертывания**. Перед шагом диспетчер ресурсов развертывания в CI/CD необходимо выполнить определенные задачи, такие как остановка и перезапуск триггеров и выполнение очистки. Рекомендуется использовать скрипты PowerShell до и после задачи развертывания. Дополнительные сведения см. в разделе [Обновление активных триггеров](#updating-active-triggers). Команда фабрики данных [предоставила сценарий](#script) для использования в нижней части этой страницы.

-   **Среды выполнения интеграции и совместное использование**. Среды выполнения интеграции не меняются часто и похожи на все этапы в CI/CD. Поэтому фабрика данных предполагает наличие одинакового имени и типа среды выполнения интеграции во всех стадиях НЕПРЕРЫВного развертывания и чтения. Если вы хотите совместно использовать среды выполнения интеграции на всех этапах, рассмотрите возможность использования фабрики ternary только для хранения общих сред выполнения интеграции. Эту общую фабрику можно использовать во всех средах в качестве связанного типа среды выполнения интеграции.

-   **Key Vault**. При использовании связанных служб, сведения о соединении которых хранятся в Azure Key Vault, рекомендуется хранить отдельные хранилища ключей для разных сред. Кроме того, можно настроить отдельные уровни разрешений для каждого хранилища ключей. Например, вы не хотите, чтобы члены команды имели разрешения для рабочих секретов. При использовании этого подхода рекомендуется иметь одинаковые имена секретов на всех этапах. Если вы сохраняете одни и те же имена секретов, вам не нужно параметризовать каждую строку подключения в средах CI/CD, так как единственное, что изменяется, — это имя хранилища ключей, которое является отдельным параметром.

## <a name="unsupported-features"></a>Неподдерживаемые функции

- Фабрика данных не допускает отбора фиксаций или выборочной публикации ресурсов. Публикации будут включать все изменения, внесенные в фабрику данных.

    - Сущности фабрики данных зависят друг от друга. Например, триггеры зависят от конвейеров, а конвейеры зависят от наборов данных и других конвейеров. Выборочная публикация подмножества ресурсов может привести к непредвиденным поведению и ошибкам.
    - В редких случаях, когда требуется Выборочная публикация, рассмотрите возможность использования исправления. Дополнительные сведения см. в [статье исправление рабочей ветви](#hotfix-production-branch).

-   Вы не можете опубликовать из частных ветвей.

-   В настоящее время вы не можете размещать проекты в BitBucket.

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a>Пример скрипта, выполняемого до и после развертывания

Следующий пример скрипта можно использовать для завершения триггеров перед развертыванием и их перезапуска. Скрипт также содержит код для удаления ресурсов. Сохраните скрипт в репозитории Azure DevOps Git и сослаться на него с помощью Azure PowerShell задачи, используя версию 4. *.

При выполнении скрипта, выполняемого перед развертыванием, в поле **аргументы скрипта** необходимо указать вариант следующих параметров.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


При выполнении скрипта, выполняемого после развертывания, в поле **аргументы скрипта** необходимо указать вариант следующих параметров.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Задача Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Ниже приведен скрипт, который можно использовать для выполнения до и после развертывания. Учетные записи для удаленных ресурсов и ссылок на ресурсы.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```
