---
title: Внедрение DevTest Labs Azure для предприятия
description: В статье представлены конкретные инструкции по внедрению DevTest Labs Azure в предприятие.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e1d119f3c7c5d6dbdb570d362c53b80dad7886bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198043"
---
# <a name="set-up-azure-devtest-labs-infrastructure-in-your-enterprise"></a>Настройка инфраструктуры DevTest Labs Azure на предприятии
Предприятиям быстро внедряют облака, из-за его [преимущества](/azure/architecture/cloud-adoption/business-strategy/cloud-migration-business-case) , которые включают динамичность, гибкость и экономики. Распространенные первым шагом для использования в облаке — начать с рабочих нагрузок разработки и тестирования.  DevTest Labs предоставляет [функции](devtest-lab-concepts.md) , преимущества enterprise и поддержки [ключевых сценариев использования и тестированию enterprise](devtest-lab-guidance-get-started.md).

При миграции этих рабочих нагрузок в облако, есть набор стандартных проблем:

- [Защита ресурсов разработки и тестирования](devtest-lab-guidance-governance-policy-compliance.md)
- [Управление и затрат](devtest-lab-guidance-governance-cost-ownership.md)
- Включение Самообслуживание для разработчиков без ущерба для безопасности предприятий и соответствие требованиям
- Автоматизация и расширение DevTest Labs для охвата дополнительных сценариев
- [Масштабирование решения на основе DevTest Labs к разнообразным ресурсам](devtest-lab-guidance-scale.md)
- [Крупномасштабное развертывание DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
- [Приступая к работе с пробной версии](devtest-lab-guidance-orchestrate-implementation.md)

## <a name="intended-audience"></a>Целевая аудитория
Документация, ориентированному на интересы предприятия предназначена для ИТ-планировщиков, архитекторов и менеджеров, отвечающих за установку и просмотра общей развертываний и отслеживает выполнение операций и рекомендации. Таким образом в этом документе особое внимание уделяется общий процесс и рекомендуется принципы проектирования, чтобы среду безопасной и стабильной разработки и тестирования, который в конечном счете управляет внедрения Azure DevTest Labs в организации.

## <a name="enterprise-customers"></a>Корпоративные клиенты

Многие текущего DevTest Labs корпоративные клиенты успешно использовать DevTest Labs, для разработки и тестирования рабочих нагрузок в своих организациях. [Узнайте больше](https://azure.microsoft.com/en-us/case-studies/?term=DevTest+labs).

## <a name="next-steps"></a>Дальнейшие действия
- [Эталонная архитектура для предприятия](devtest-lab-reference-architecture.md)
