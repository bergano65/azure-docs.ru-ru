---
title: Использование Машинное обучение Azure в предварительной версии Azure Notebook
description: Обзор примеров записных книжек для Машинное обучение Azure, которые можно использовать с предварительной версией Azure Notebooks.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c0e0e9ccea079d7e8f4e35e9af2a0e1c1ec5051
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646958"
---
# <a name="use-azure-machine-learning-in-azure-notebooks-preview"></a>Использование Машинное обучение Azure в предварительной версии Azure Notebook

В записных книжках Azure предварительно настроена необходимая среда для работы с [машинное обучение Azure](/azure/machine-learning/service/). Можно легко клонировать пример проекта в свою учетную запись Записных книжек для изучения различных сценариев Машинного обучения.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-the-sample-into-your-account"></a>Клонирование примера в свою учетную запись

1. Войдите в [Записные книжки Azure](https://notebooks.azure.com/).
1. Выберите **Мои проекты** , чтобы открыть панель мониторинга проектов.
1. Щелкните элемент **Отправить** репозиторий GitHub (кнопка со стрелкой вверх), чтобы открыть всплывающее окно **Отправка репозитория GitHub** .
1. В всплывающем окне введите `Azure/MachineLearningNotebooks` в **репозитории GitHub**, укажите имя проекта в **имени проекта** , например "машинное обучение Azure", укажите идентификатор в поле **идентификатор проекта**, снимите флажок **общедоступный** , если требуется, а затем выберите **Импорт**.

    ![Импорт примера записной книжки Машинного обучения Azure в учетную запись службы "Записные книжки"](media/azureml-import-project.png)

1. Через несколько минут служба "Записные книжки Azure" автоматически перенаправит вас на панель мониторинга нового проекта.

## <a name="run-a-sample-notebook"></a>Запуск примера записной книжки

1. Выберите **00 - configuration.ipynb**, чтобы открыть раздел конфигурации записной книжки, и следуйте его инструкциям, чтобы создать рабочую область машинного обучения Azure.

    - Так как служба "Записные книжки Azure" уже содержит необходимые пакеты Python, можно просто запустить фрагмент кода на шаге 2 предварительных требований, чтобы проверить версию пакета SDK для Azure ML.

1. После завершения настройки выберите **01. Приступая к работе** , чтобы открыть папку, содержащую тринадцать различные примеры записных книжек, каждая из которых является ясной.

## <a name="next-steps"></a>Дальнейшие действия

Документация по Машинное обучение Azure содержит различные другие ресурсы, которые помогут вам работать с Машинное обучение в записных книжках.

- [Краткое руководство. Использование Python для начала работы с Машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Учебник #1: обучение модели классификации изображений с помощью Машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Руководство #2. Развертывание модели классификации изображений в экземпляре контейнера Azure (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Учебник. Обучение модели классификации с помощью автоматизированного машинного обучения в Машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Ознакомьтесь также с документацией по [пакету SDK Python для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
