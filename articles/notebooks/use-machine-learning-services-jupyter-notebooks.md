---
title: Работа со Службами машинного обучения Azure в Записных книжках Azure
description: Обзор примеров записных книжек для Служб машинного обучения Azure, которые можно использовать с Записными книжками Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f591758fa6e51c420a090aa62d5160320fe15fe8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973033"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Работа со Службами машинного обучения Azure в записной книжке

Служба "Записные книжки Azure" предварительно настроена со средой, необходимой для работы со [Службой машинного обучения Azure](/azure/machine-learning/service/). Можно легко клонировать пример проекта в свою учетную запись Записных книжек для изучения различных сценариев Машинного обучения.

## <a name="clone-the-sample-into-your-account"></a>Клонирование примера в свою учетную запись

1. Войдите в [Записные книжки Azure](https://notebooks.azure.com/).
1. Выберите **Мои проекты** , чтобы открыть панель мониторинга проектов.
1. Щелкните элемент **Отправить** репозиторий GitHub (кнопка со стрелкой вверх), чтобы открыть всплывающее окно **Отправка репозитория GitHub** .
1. Во всплывающем окне введите `Azure/MachineLearningNotebooks` в поле **GitHub repository** (Репозиторий GitHub), укажите имя проекта в поле **Имя проекта**, например "Служба машинного обучения Azure", и идентификатор в поле **Идентификатор проекта**, снимите флажок **Общедоступный**, а затем выберите **Импорт**.

    ![Импорт примера записной книжки Машинного обучения Azure в учетную запись службы "Записные книжки"](media/azureml-import-project.png)

1. Через несколько минут служба "Записные книжки Azure" автоматически перенаправит вас на панель мониторинга нового проекта.

## <a name="run-a-sample-notebook"></a>Запуск примера записной книжки

1. Выберите **00 - configuration.ipynb**, чтобы открыть раздел конфигурации записной книжки, и следуйте его инструкциям, чтобы создать рабочую область машинного обучения Azure.

    - Так как служба "Записные книжки Azure" уже содержит необходимые пакеты Python, можно просто запустить фрагмент кода на шаге 2 предварительных требований, чтобы проверить версию пакета SDK для Azure ML.

1. После завершения настройки выберите **01. Приступая к работе** , чтобы открыть папку, содержащую тринадцать различные примеры записных книжек, каждая из которых является ясной.

## <a name="next-steps"></a>Следующие шаги

Документация Служб машинного обучения Azure содержит множество других ресурсов, которые помогут вам при работе со Службой машинного обучения в записных книжках:

- [Краткое руководство по началу работы со службой "Машинное обучение Azure" с использованием пакета SDK для Python](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python).
- [Руководство (1) по обучению модели классификации изображений с помощью Службы машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml).
- [Руководство (2). Развертывание модели классификации изображений в Экземпляре контейнера Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml).
- [Руководство. Создание регрессионной модели с помощью автоматического машинного обучения](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models).

Ознакомьтесь также с документацией по [пакету SDK Python для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
