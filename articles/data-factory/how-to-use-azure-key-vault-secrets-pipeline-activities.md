---
title: Использование секретов Azure Key Vault в действиях конвейера
description: Узнайте, как извлечь сохраненные учетные данные из хранилища ключей Azure и использовать их во время выполнения конвейера фабрики данных.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: f2531ebfd8b1eafc04fa6eda660b0eec3d1147f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417075"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Использование секретов Azure Key Vault в действиях конвейера

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Можно сохранить учетные данные или секретные значения в Azure Key Vault и использовать их во время выполнения конвейера для передачи в действия.

## <a name="prerequisites"></a>Предварительные условия

Эта функция основана на управляемом удостоверении фабрики данных.  Узнайте, как это работает из [управляемого удостоверения для фабрики данных](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) и убедитесь, что фабрика данных имеет одну связь.

## <a name="steps"></a>Шаги

1. Откройте свойства фабрики данных и скопируйте значение идентификатора приложения управляемого удостоверения.

    ![Значение управляемого идентификатора](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Откройте политики доступа к хранилищу ключей и добавьте разрешения управляемого удостоверения для получения и перечисления секретов.

    ![Политики доступа Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Политики доступа Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Нажмите кнопку **Добавить**, а затем — **сохранить**.

3. Перейдите к Key Vault секрету и скопируйте идентификатор секрета.

    ![Идентификатор секрета](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Запишите URI секрета, который вы хотите получить во время выполнения конвейера фабрики данных.

4. В конвейере фабрики данных добавьте новое веб-действие и настройте его следующим образом.  

    |Свойство  |Значение  |
    |---------|---------|
    |Безопасный вывод     |True         |
    |URL-адрес     |[Значение URI секрета]? API-Version = 7.0         |
    |Метод     |GET         |
    |Проверка подлинности     |MSI         |
    |Ресурс        |https://vault.azure.net       |

    ![Веб-действие](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Необходимо добавить **? API-Version = 7.0** в конец URI секрета.  

    > [!CAUTION]
    > Задайте для параметра безопасный вывод значение true, чтобы предотвратить запись секретного значения в обычный текст.  Для всех дальнейших действий, использующих это значение, параметр Secure input должен иметь значение true.

5. Чтобы использовать значение в другом действии, используйте следующее выражение ** @activityкода ("web1"). Output. Value**.

    ![Выражение кода](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Дальнейшие шаги

Сведения об использовании Azure Key Vault для хранения учетных данных для хранилищ данных и вычислений см. [в разделе Хранение учетных данных в Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
