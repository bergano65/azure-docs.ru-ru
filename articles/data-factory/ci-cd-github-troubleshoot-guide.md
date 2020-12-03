---
title: Устранение неполадок непрерывной интеграции — CD, Azure DevOps и GitHub в ADF
description: Используйте различные методы для устранения неполадок с CI-CD в ADF.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 12/03/2020
ms.openlocfilehash: d5cf39cf91b69574fc1423cfe0942b567cf9a278
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548214"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Устранение неполадок непрерывной интеграции — CD, Azure DevOps и GitHub в ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье рассматриваются распространенные методы устранения неполадок непрерывного развертывания Integration-Continuous (CI-CD), Azure DevOps и GitHub в фабрике данных Azure.

Если у вас возникли вопросы или проблемы при использовании методов управления версиями или DevOps, вот несколько статей, которые могут оказаться полезными:

- Сведения об использовании системы управления версиями в ADF см. в статье [Управление версиями в ADF](source-control.md) . 
- Дополнительные сведения о том, как DevOps CI-CD в ADF, см. в разделе  [CI-CD в ADF](continuous-integration-deployment.md) .
 
## <a name="common-errors-and-messages"></a>Распространенные ошибки и сообщения

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Не удалось подключиться к репозиторию Git из-за другого клиента

#### <a name="issue"></a>Проблема
    
Иногда возникают проблемы проверки подлинности, такие как состояние HTTP 401. Особенно если у вас есть несколько клиентов с гостевой учетной записью, то некоторые из них могут стать более сложными.

#### <a name="cause"></a>Причина

Мы наблюдали, что маркер был получен от исходного клиента, но ADF-файл находится в гостевой клиенте и пытается использовать маркер для посещения DevOps в гостевом клиенте. Это не является ожидаемым поведением.

#### <a name="recommendation"></a>Рекомендация

Вместо этого следует использовать маркер, выданный от гостевого клиента. Например, необходимо назначить тот же Azure Active Directory в качестве гостевого клиента, а также DevOps, чтобы он мог правильно установить поведение маркера и использовать правильный клиент.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Недопустимые параметры шаблона в файле параметров

#### <a name="issue"></a>Проблема

Если удалить триггер в ветви dev, который уже доступен в тестовой или рабочей ветви с **такой же** конфигурацией (например, частота и интервал), то развертывание конвейера выпуска завершится, и соответствующий триггер будет удален в соответствующих средах. Но если у вас есть **другая** конфигурация (например, частота и интервал) для триггера в тестовой или рабочей среде и при удалении одного и того же триггера в разработке, развертывание завершается ошибкой.

#### <a name="cause"></a>Причина

Конвейер CI/CD завершается со следующей ошибкой:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Рекомендация

Эта ошибка возникает из-за того, что мы часто удаляем триггер, который является параметризованным, поэтому параметры не будут доступны в шаблоне ARM (поскольку триггер больше не существует). Так как этот параметр больше не находится в шаблоне ARM, необходимо обновить переопределенные параметры в конвейере DevOps. В противном случае каждый раз, когда параметры в шаблоне ARM изменятся, они должны обновить переопределенные параметры в конвейере DevOps (в задаче развертывания).

### <a name="updating-property-type-is-not-supported"></a>Обновление типа свойства не поддерживается

#### <a name="issue"></a>Проблема

Конвейер выпуска CI/CD завершился со следующей ошибкой:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Причина

Это происходит из-за Integration Runtime с тем же именем в целевой фабрике, но с другим типом. При развертывании Integration Runtime должны иметь один и тот же тип.

#### <a name="recommendation"></a>Рекомендация

- Ознакомьтесь с рекомендациями по непрерывной интеграции и перекомпакт-дискам:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Среды выполнения интеграции не меняются часто и похожи на все этапы в CI/CD, поэтому фабрика данных предполагает наличие одинакового имени и типа среды выполнения интеграции на всех стадиях НЕПРЕРЫВного развертывания и компакт-диска. Если имя и типы & свойства различаются, убедитесь, что они соответствуют исходной и целевой конфигурации IR, а затем разверните конвейер выпуска.
- Если вы хотите совместно использовать среды выполнения интеграции на всех этапах, рассмотрите возможность использования тернарной фабрики только для хранения общих сред выполнения интеграции. Эту общую фабрику можно использовать во всех средах в качестве связанного типа среды выполнения интеграции.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Не удалось создать или обновить документ из-за недопустимой ссылки

#### <a name="issue"></a>Проблема

При попытке публикации изменений в фабрике данных появляется следующее сообщение об ошибке:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>Симптом

Вы отсоединили конфигурацию Git и настроили ее снова с выбранным флагом "Импорт ресурсов", который задает для фабрики данных значение "синхронизировано". Это означает отсутствие изменений для публикации.

#### <a name="resolution"></a>Разрешение

Отсоедините конфигурацию Git и снова настройте ее и убедитесь, что не установлен флажок "импортировать существующие ресурсы".

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Сбой перемещения фабрики данных из одной группы ресурсов в другую

#### <a name="issue"></a>Проблема

Не удается переместить фабрику данных из одной группы ресурсов в другую, сбой со следующей ошибкой:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Разрешение

Чтобы разрешить операцию перемещения, необходимо удалить службы SSIS — IR и Shared IRs. Если вы не хотите удалять IRs, лучше всего следовать документу копирование и клонирование, чтобы скопировать и после этого удалить старую фабрику данных.

###  <a name="unable-to-export-and-import-arm-template"></a>Не удалось экспортировать и импортировать шаблон ARM

#### <a name="issue"></a>Проблема

Не удалось экспортировать и импортировать шаблон ARM. На портале нет ошибок. Однако в трассировке браузера может отобразиться следующая ошибка:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Причина

Вы создали роль клиента в качестве пользователя и у нее нет необходимых разрешений. При загрузке фабрики в пользовательском интерфейсе проверяется ряд значений элементов управления экспозицией для фабрики. В этом случае роль доступа пользователя не имеет разрешения на доступ к API *куерифеатуресвалуе* . Для доступа к этому API функция глобальных параметров отключена. Путь к коду экспорта ARM частично полагается на функцию глобальных параметров.

#### <a name="resolution"></a>Разрешение

Чтобы устранить эту проблему, необходимо добавить в роль следующее разрешение: *Microsoft. куерифеатуресвалуе, фабрики,/Action*. Это разрешение должно включаться по умолчанию в роли "участник фабрики данных".

## <a name="next-steps"></a>Дальнейшие шаги

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог о Фабрике данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы на добавление функции в Фабрику данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видео по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум о переполнении стека для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о Фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
