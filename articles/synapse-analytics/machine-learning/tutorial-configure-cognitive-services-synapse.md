---
title: Руководство по необходимым компонентам для Cognitive Services в Azure Synapse
description: Учебник по настройке необходимых компонентов для использования Cognitive Services в Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: eef65db05ab94b5b8de5ff82c2c51dba0730f170
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222179"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Руководство по необходимым компонентам для использования Cognitive Services в Azure Synapse

Из этого учебника вы узнаете, как настроить необходимые компоненты для безопасного использования Cognitive Services в Azure Synapse.

Темы, рассматриваемые в этом руководстве:
> [!div class="checklist"]
> - Создание ресурсов Cognitive Services. Например, Анализ текста или Детектор аномалий.
> - Сохранение ключа проверки подлинности в ресурсах Cognitive Services в виде секретов в хранилище Key Vault и настройка доступа для рабочей области Azure Synapse.
> - Создание связанной службы Azure Key Vault в рабочей области Azure Synapse Analytics.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись, прежде чем начинать работу](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Предварительные требования

- [Рабочая область Azure Synapse Analytics](../get-started-create-workspace.md) с учетной записью хранения ADLS 2-го поколения, настроенной в качестве хранилища по умолчанию. Необходимо быть **участником для данных BLOB-объектов хранилища** файловой системы ADLS 2-го поколения, с которой вы работаете.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>Создание ресурса Cognitive Services

[Azure Cognitive Services](../../cognitive-services/index.yml) содержит множество различных типов служб. Ниже приведены некоторые примеры, используемые в учебниках по Synapse.

### <a name="create-an-anomaly-detector-resource"></a>Создание ресурса "Детектор аномалий"
Создайте [Детектор аномалий](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) на портале Azure.

![Создание детектора аномалий](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Создание ресурса "Анализ текста"
Создайте ресурс [Анализ текста](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) на портале Azure.

![Создание Анализа текста](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Создание Key Vault, настройка секретов и доступа

1. Создайте [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault) на портале Azure.
2. Выберите **Key Vault > Политики доступа** и предоставьте [рабочей области Azure Synapse](../security/synapse-workspace-managed-identity.md) MSI разрешения для чтения секретов из Azure Key Vault.

>Убедитесь, что изменения политики сохранены. Этот важный шаг легко пропустить.

![Добавление политики доступа](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Перейдите к ресурсу Cognitive Service, например, выберите **Детектор аномалий > Keys and Endpoint** (Ключи и конечная точка) и скопируйте один из двух ключей в буфер обмена.

4. Чтобы создать секрет, выберите **Key Vault -> Секрет**. Укажите имя секрета, а затем вставьте ключ из предыдущего шага в поле "Значение". Наконец, нажмите кнопку **Создать**.

![Создание секрета](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Обязательно запомните или запишите имя секрета. Оно будет использоваться позже при подключении к Cognitive Services из Azure Synapse Studio.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Создание связанной службы Azure Key Vault в Azure Synapse

1. Откройте рабочую область в Azure Synapse Studio. Выберите **Управление > Связанные службы**. Создайте связанную службу Azure Key Vault, указывающую на только что созданный экземпляр Key Vault. Затем проверьте подключение, нажав кнопку "Проверить подключение" и убедившись, что она засветилась зеленым цветом. Если все работает нормально, щелкните "Создать", а затем — "Опубликовать все", чтобы сохранить изменения.
![Связанная служба](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Теперь вы можете продолжить работу с одним из учебников по использованию интерфейса Azure Cognitive Services в Azure Synapse Studio.

## <a name="next-steps"></a>Дальнейшие действия

- [Учебник. Анализ тональности с помощью Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Учебник. Обнаружение аномалий с помощью Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Учебник. Оценка моделей машинного обучения в выделенных пулах Azure Synapse SQL](tutorial-sql-pool-model-scoring-wizard.md).
- [Возможности машинного обучения в Azure Synapse Analytics](what-is-machine-learning.md)