---
title: Руководство. Анализ тональности с помощью Cognitive Services
description: Узнайте, как выполнять анализ тональности в Synapse с помощью Cognitive Services
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 1b407cbee5218149f794ab125ac058e32b422558
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465223"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Руководство. Анализ тональности с помощью Cognitive Services (предварительная версия)

В этом руководстве показано, как дополнить данные в Azure Synapse с помощью [Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492). Для выполнения анализа тональности мы будем использовать возможности [Анализа текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/). Чтобы дополнить данные сведениями о тональности, пользователь в Azure Synapse может просто выбрать таблицу с текстовым столбцом. При этом определяются позитивные, негативные, смешанные и нейтральные тональности с оценкой вероятности.

Темы, рассматриваемые в этом руководстве:

> [!div class="checklist"]
> - получение набора данных из таблицы Spark, содержащей текстовый столбец, для анализа тональности;
> - использование интерфейса мастера в Azure Synapse для дополнения данных с помощью Анализа текста в Cognitive Services.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись, прежде чем начинать работу](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Предварительные требования

- [Рабочая область Azure Synapse Analytics](../get-started-create-workspace.md) с учетной записью хранения ADLS 2-го поколения, настроенной в качестве хранилища по умолчанию. Необходимо быть **участником для данных BLOB-объектов хранилища** файловой системы ADLS 2-го поколения, с которой вы работаете.
- Пул Spark в рабочей области Azure Synapse Analytics. Дополнительные сведения см. в статье [Создание пула Spark в Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Прежде чем приступить к работе с этим руководством, нужно выполнить описанные здесь шаги предварительной настройки. [Настройка Cognitive Services для Azure Synapse.](tutorial-configure-cognitive-services-synapse.md)

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Создание таблицы Spark

Для работы с этим руководством вам потребуется таблица Spark.

1. Скачайте следующий CSV-файл с набором данных для анализа текста: [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv).

1. Отправьте этот файл в учетную запись хранения Azure Synapse ADLS 2-го поколения.
![Отправка данных](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Создайте таблицу Spark из CSV-файла, щелкнув этот файл правой кнопкой мыши и выбрав пункт **Создать записную книжку -> Создать таблицу Spark**.
![Создание таблицы Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Присвойте имя таблице в ячейке кода и выполните записную книжку в пуле Spark. Не забудьте установить параметр header = True.
![Выполнение записной книжки](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>Запуск мастера Cognitive Services

1. Щелкните правой кнопкой мыши таблицу Spark, созданную на предыдущем шаге. Выберите "Машинное обучение"-> "Дополнить с использованием существующей модели".
![Запуск мастера оценки](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Появится панель конфигурации и вам будет предложено выбрать модель Cognitive Services. Выберите "Анализ текста — Анализ тональности".

![Запуск мастера оценки — шаг 1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Предоставление сведений для проверки подлинности

Для выполнения проверки подлинности в Cognitive Services нужно предоставить ссылку на нужный секрет, размещенный в Key Vault. Указанные ниже входные данные зависят от [предварительных шагов](tutorial-configure-cognitive-services-synapse.md), которые должны быть выполнены до этого шага.

- **Подписка Azure**. Выберите подписку Azure, к которой принадлежит ваше хранилище ключей.
- **Учетная запись Cognitive Services.** Это ресурс Анализа текста, к которому вы собираетесь подключиться.
- **Связанная служба Azure Key Vault.** Во время выполнения предварительных шагов вы создали связанную службу для ресурса Анализа текста. Выберите ее.
- **Имя секрета.** Это имя секрета в хранилище ключей, содержащего ключ для проверки подлинности в ресурсе Cognitive Services.

![Предоставление сведений об Azure Key Vault](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Настройка анализа тональности

Теперь вам следует настроить анализ тональности. Предоставьте следующие данные.
- **Язык**: Выберите язык текста, на котором нужно выполнять анализ тональности. Выберите **EN**.
- **Текстовый столбец.** Это текстовый столбец в наборе данных, который будет анализироваться для определения тональности. Выберите в таблице столбец **comment**.

Затем щелкните **Открыть записную книжку**. Будет создана записная книжка с кодом PySpark, который выполняет анализ тональности с помощью Azure Cognitive Services.

![Настройка анализа тональности](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>Открытие и запуск записной книжки

Записная книжка, которую вы только что открыли, использует [библиотеку mmlspark](https://github.com/Azure/mmlspark) для подключения к Cognitive Services.

Предоставленные сведения об Azure Key Vault позволяют безопасно сослаться на секреты из этого интерфейса, не раскрывая их.

Теперь вы можете выбрать **Выполнить все**, чтобы дополнить данные сведениями о тональности. Сведения о тональности возвращаются в виде меток "Позитивная", "Негативная", "Нейтральная" и "Смешанная" с оценкой вероятности для каждой из них. [Дополнительные сведения об анализе тональности в Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147792).

![Выполнение анализа тональности](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Дальнейшие действия
- [Учебник. Обнаружение аномалий с помощью Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Учебник. Оценка моделей машинного обучения в выделенных пулах Azure Synapse SQL](tutorial-sql-pool-model-scoring-wizard.md)
- [Возможности машинного обучения в Azure Synapse Analytics](what-is-machine-learning.md)