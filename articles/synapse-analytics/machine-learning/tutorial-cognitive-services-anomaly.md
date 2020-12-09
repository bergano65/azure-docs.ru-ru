---
title: Руководство по обнаружению аномалий с помощью Cognitive Services
description: Учебник по обнаружению аномалий в Synapse с помощью Cognitive Services
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4052d6a0773aa27e0a378ee04975c7946f1ffbfe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464461"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Руководство по обнаружению аномалий с помощью Cognitive Services (предварительная версия)

Из этого учебника вы узнаете, как обогатить данные в Azure Synapse с помощью [Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492). Для обнаружения аномалий мы будем использовать [Детектор аномалий](https://go.microsoft.com/fwlink/?linkid=2147493). Чтобы обнаруживать аномалии, пользователь в Azure Synapse может просто выбрать таблицу для обогащения.

Темы, рассматриваемые в этом руководстве:

> [!div class="checklist"]
> - Шаги для получения набора данных таблицы Spark, содержащего данные временных рядов.
> - Использование интерфейса мастера в Azure Synapse для обогащения данных с помощью Детектора аномалий в Cognitive Service.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись, прежде чем начинать работу](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Предварительные требования

- [Рабочая область Azure Synapse Analytics](../get-started-create-workspace.md) с учетной записью хранения ADLS 2-го поколения, настроенной в качестве хранилища по умолчанию. Необходимо быть **участником для данных BLOB-объектов хранилища** файловой системы ADLS 2-го поколения, с которой вы работаете.
- Пул Spark в рабочей области Azure Synapse Analytics. Дополнительные сведения см. в статье [Создание пула Spark в Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Прежде чем приступить к работе с этим учебником, необходимо выполнить описанные здесь шаги предварительной настройки. [Настройка Cognitive Services для Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Создание таблицы Spark

Для выполнения инструкций из этого учебника вам потребуется таблица Spark.

1. Скачайте следующий файл записной книжки, содержащий код для создания таблицы Spark: [prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Отправьте файл в рабочую область Azure Synapse.
![Отправка записной книжки](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Откройте файл записной книжки и выберите ячейку **Выполнить все**.
![Создание таблицы Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Таблица Spark с именем **anomaly_detector_testing_data** теперь должна отображаться в базе данных Spark по умолчанию.

## <a name="launch-cognitive-services-wizard"></a>Запуск мастера Cognitive Services

1. Щелкните правой кнопкой мыши таблицу Spark, созданную на предыдущем шаге. Выберите "Машинное обучение" -> Enrich with existing model (Обогатить с помощью существующей модели).
![Запуск мастера оценки](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Появится панель конфигурации, и вам будет предложено выбрать модель Cognitive Services. Выберите "Детектор аномалий".

![Запуск мастера оценки. Шаг 1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Предоставление сведений о проверке подлинности

Для выполнения проверки подлинности в Cognitive Services необходимо сослаться на секрет, который будет использоваться в Key Vault. Указанные ниже входные данные зависят от [предварительных шагов](tutorial-configure-cognitive-services-synapse.md), которые должны быть выполнены до этого шага.

- **Подписка Azure**. Выберите подписку Azure, к которой принадлежит ваше хранилище ключей.
- **Учетная запись Cognitive Services**. Это ресурс "Анализ текста", к которому вы собираетесь подключиться.
- **Связанная служба Azure Key Vault**. Во время выполнения предварительных шагов вы создали связанную службу для ресурса "Анализ текста". Выберите ее.
- **Имя секрета**. Это имя секрета в хранилище ключей, содержащего ключ для проверки подлинности в ресурсе Cognitive Services.

![Предоставление сведений об Azure Key Vault](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Настройка обнаружения аномалий

Далее необходимо настроить обнаружение аномалий. Укажите следующие сведения:

- Степень детализации: скорость выборки данных. Например, если данные имеют значение для каждой минуты, степень детализации будет поминутной. Выберите **ежемесячно**. 

- Timestamp: столбец, представляющий время ряда. Выберите столбец **метка времени**.

- Значение временных рядов: столбец, представляющий значение ряда в момент времени, указанный в столбце "Метка времени". Выберите столбец **значение**.

- Группирование: столбец, группирующий ряды. То есть все строки, имеющие одно и то же значение в этом столбце, должны формировать один временной ряд. Выберите столбец **группа**.

Затем выберите **Открыть записную книжку**. Будет создана записная книжка с кодом PySpark, который выполняет обнаружение аномалий с помощью Azure Cognitive Services.

![Настройка детектора аномалий](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Открытие и запуск записной книжки

Записная книжка, которую вы только что открыли, использует [библиотеку mmlspark](https://github.com/Azure/mmlspark) для подключения к Cognitive Services.

Предоставленные сведения об Azure Key Vault позволяют безопасно сослаться на секреты из этого интерфейса, не раскрывая их.

Теперь можно **выполнить все** ячейки, чтобы запустить обнаружение аномалий. Дополнительные сведения о [Детекторе аномалий в Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147493).

![Запуск обнаружения аномалий](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Учебник. Анализ тональности с помощью Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Учебник. Оценка моделей машинного обучения в выделенных пулах Azure Synapse SQL](tutorial-sql-pool-model-scoring-wizard.md).
- [Возможности Машинного обучения в Azure Synapse Analytics](what-is-machine-learning.md)