---
title: Что такое среды машинного обучения
titleSuffix: Azure Machine Learning
description: В этой статье вы узнаете о преимуществах сред машинного обучения, которые позволяют воспроизводимые, отслеживаемые и переносимые определения зависимостей в различных целевых вычислительных ресурсах.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: ad520c7e6503f28de0bd5538662c223575a078fa
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692736"
---
# <a name="what-are-azure-machine-learning-environments"></a>Что такое Машинное обучение Azure среды?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В средах указываются пакеты Python, переменные среды и параметры программного обеспечения для сценариев обучения и оценки, а также время выполнения (Python, Spark или DOCKER). Это управляемые и версии сущностей в рабочей области Машинное обучение Azure, которые позволяют воспроизводимые, отслеживаемые и переносимые рабочие процессы машинного обучения в различных целевых объектах вычислений.

Объект среды можно использовать в локальных средах вычислений для разработки сценария обучения, повторного использования этой же среды на Машинное обучение Azure вычислений для обучения модели и даже для развертывания модели в этой же среде.

Ниже показано, что один и тот же объект среды можно использовать в конфигурации запуска для обучения, а также в конфигурации определения и развертывания для развертываний веб-служб.

![Схема окружения в рабочем процессе машинного обучения](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Типы окружений

Среды можно разделить на три категории: **проверенные**, **управляемые пользователем** и **управляемые системой**.

Проверенные среды предоставляются Машинное обучение Azure и доступны в рабочей области по умолчанию. Они содержат коллекции пакетов и параметров Python, которые помогут начать работу с различными платформами машинного обучения. 

В среде, управляемой пользователем, вы несете ответственность за настройку среды и установку каждого пакета, который необходим вашему обучающему сценарию на целевом объекте вычислений. Conda не будет проверять вашу среду или устанавливать что-либо за вас. Обратите внимание, что при определении собственной среды необходимо перечислить `azureml-defaults` с версией `>= 1.0.45` в качестве зависимости PIP. Этот пакет содержит функции, необходимые для размещения модели в качестве веб-службы.

Среды, управляемые системой, используются, когда требуется [Conda](https://conda.io/docs/) управлять средой Python и зависимостями скриптов. По умолчанию служба использует этот тип окружения из-за его полезности в удаленных целевых объектах вычислений, которые не настраиваются вручную.

## <a name="creating-and-managing-environments"></a>Создание сред и управление ими

Среды могут создаваться с помощью:

* Определение новых `Environment` объектов либо с помощью проверенной среды, либо путем определения собственных зависимостей
* Использование существующих объектов `Environment` из рабочей области. Это обеспечивает согласованность и воспроизводимость с зависимостями
* Импорт из существующего определения среды Anaconda.

Конкретные примеры кода см. [в этой статье](how-to-use-environments.md#create-an-environment) . Среда также легко управляется в рабочей области и включает следующие функциональные возможности:

* При отправке эксперимента среды автоматически регистрируются в рабочей области. Их также можно зарегистрировать вручную
* Извлеките среды из рабочей области и используйте их для обучения, развертывания или внесения изменений в определение среды.
* Управление версиями позволяет просматривать изменения в средах с течением времени и гарантирует воспроизводимость
* Автоматическое создание образов DOCKER из ваших сред

Примеры кода см. в разделе Manage Environments ( [Управление средами](how-to-use-environments.md#manage-environments) ).

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте [, как создавать и использовать среды](how-to-use-environments.md) в машинное обучение Azure
* См. справочную документацию по пакету SDK Python для [класса Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* См. справочную документацию по пакету SDK для R для [сред](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).