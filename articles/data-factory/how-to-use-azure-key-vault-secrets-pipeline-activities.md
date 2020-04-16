---
title: Использование секретов Azure Key Vault в действиях конвейера
description: Узнайте, как получить сохраненные учетные данные из хранилища ключей Azure и использовать их во время запуска конвейера data Factory.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417075"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Использование секретов Azure Key Vault в действиях конвейера

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Вы можете хранить учетные данные или секретные значения в Хранилище ключей Azure и использовать их во время выполнения конвейера для передачи вашей деятельности.

## <a name="prerequisites"></a>Предварительные требования

Эта функция основана на управляемой итоге фабрики данных.  Узнайте, как это работает с [управляемой идентификацией для Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) и убедитесь, что на фабрике данных есть одна связанная.

## <a name="steps"></a>Шаги

1. Откройте свойства фабрики данных и скопируйте значение идентификатора управляемого identity.s.

    ![Управляемое значение идентификации](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Откройте политики доступа к хранилищам ключей и добавьте управляемые разрешения на идентификацию для получения и списка секретов.

    ![Политики доступа Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Политики доступа Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Нажмите **Добавить,** а затем нажмите **Сохранить**.

3. Перейдите к секрету Key Vault и скопируйте секретный идентификатор.

    ![Секретный идентификатор](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Обратите внимание на ваш секретный URI, который вы хотите получить во время выполнения конвейера data Factory.

4. В конвейере Data Factory добавьте новую веб-активность и настройте ее следующим образом.  

    |Свойство  |Значение  |
    |---------|---------|
    |Безопасный выход     |True         |
    |URL-адрес     |«Ваше секретное значение URI»?api-версия 7.0         |
    |Метод     |GET         |
    |Аутентификация     |MSI         |
    |Ресурс        |https://vault.azure.net       |

    ![Веб-действие](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Вы должны добавить **?api-версию 7.0** к концу вашего секретного URI.  

    > [!CAUTION]
    > Установите опцию Secure Output, чтобы предотвратить ввоза секретного значения в простой текст.  Любые дальнейшие действия, потребляющие это значение, должны иметь свой параметр безопасного ввода.

5. Чтобы использовать значение в другом действии, используйте следующее выражение ** @activityкода ('Web1').output.value**.

    ![Выражение кода](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как использовать Azure Key Vault для хранения учетных данных для хранилищ данных и вычислений, смотрите [учетные данные Хранилища в Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
