---
title: Что происходит с Azure Batch AI | Документация Майкрософт
description: Сведения о том, что происходит с Azure Batch AI, и описание объекта вычислений, доступного в Службе машинного обучения Azure.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436881"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Что происходит с Azure Batch AI

**Служба Azure Batch AI прекращает работу в марте.** Возможности обучения и оценки службы Batch AI для масштабных развертываний теперь доступны в [Службе машинного обучения Azure](../machine-learning/service/overview-what-is-azure-ml.md), которая стала общедоступной 4 декабря 2018 года.

Помимо множества других возможностей машинного обучения, Служба машинного обучения Azure включает облачный управляемый целевой объект вычислений для обучения, развертывания и оценки моделей машинного обучения. Этот целевой объект вычислений называется [Вычислительная среда Машинного обучения Azure](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Начните миграцию и перейдите к использованию уже сегодня](#migrate). Вы можете взаимодействовать со Службой машинного обучения Azure через [пакеты SDK для Python](../machine-learning/service/quickstart-create-workspace-with-python.md), интерфейс командной строки и [портал Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Сроки поддержки

| Дата | Сведения о поддержке службы Batch AI |
| ---- |-----------------|
| &nbsp;14&nbsp;декабря 2018 г.| Продолжайте использовать существующие подписки Azure Batch AI как раньше. Однако регистрация **новых подписок** больше не возможна, и в эту службу больше не будут вкладываться новые инвестиции.|
| &nbsp;31&nbsp;марта 2019 г. | После этой даты существующие подписки Batch AI больше не будут работать. |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>Как выполнить миграцию

Чтобы избежать нарушений в работе приложений и использовать преимущества новейших функций, выполните следующие действия до 31 марта 2019 г.:

1. Создайте рабочую область Службы машинного обучения Azure и начните работу:
    + [Краткое руководство с описанием использования Python](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Краткое руководство с описанием использования портала Azure](../machine-learning/service/quickstart-get-started.md)

1. Настройте [Вычислительную среду Машинного обучения Azure](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) для обучения моделей.

1. Обновите скрипты для использования Вычислительной среды Машинного обучения Azure.

## <a name="support"></a>Поддержка

Поддержка доступна для существующих клиентов, которые хотят перейти на более комплексную [Службу машинного обучения Azure](https://aka.ms/aml-docs).

Если Служба машинного обучения Azure не соответствует вашим потребностям, а поддерживаемая функция существует в службе Batch AI, отправьте запрос на поддержку Batch AI в группу поддержки, чтобы они разрешили вашей подписке использовать Batch AI до прекращения поддержки службы.

## <a name="next-steps"></a>Дополнительная информация

+ Ознакомьтесь со статьей [Что такое Служба машинного обучения Azure](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Настройте целевой объект вычислений для обучения моделей с помощью Службы машинного обучения Azure](../machine-learning/service/how-to-set-up-training-targets.md).

+ Просмотрите [стратегию развития Azure](https://azure.microsoft.com/updates/), чтобы узнать о других обновлениях служб Azure.
