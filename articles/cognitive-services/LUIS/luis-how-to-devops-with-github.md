---
title: Как применить DevOps с GitHub-LUIS
titleSuffix: Azure Cognitive Services
description: Примените DevOps с Language Understanding (LUIS) и GitHub.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: b733e90b69be4e2bd458be5486564747ed4cca78
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018877"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>Применение DevOps при разработке приложений LUIS с помощью GitHub Actions

В [репозитории шаблонов Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) можно найти полное решение, в котором реализованы рекомендации по DevOps и проектированию программного обеспечения для Luis. Этот репозиторий шаблонов можно использовать для создания собственного репозитория со встроенной поддержкой рабочих процессов CI/CD и методов, позволяющих [управлять исходным кодом](luis-concept-devops-sourcecontrol.md), [автоматизировать сборки](luis-concept-devops-automation.md), [тестирование](luis-concept-devops-testing.md)и [Управление выпусками](luis-concept-devops-automation.md#release-management) с помощью Luis для собственного проекта.

## <a name="the-luis-devops-template-repo"></a>Репозиторий шаблонов DevOps LUIS

[Репозиторий шаблонов DevOps Luis](https://github.com/Azure-Samples/LUIS-DevOps-Template) содержит инструкции по следующим действиям:

* **Клонирование репозитория шаблонов** . Копирование шаблона в собственный репозиторий GitHub.
* **Настройка ресурсов Luis** . Создайте [ресурсы для создания и прогнозирования Luis в Azure](./luis-how-to-azure-subscription.md) , которые будут использоваться рабочими процессами непрерывной интеграции.
* **Настройка рабочих процессов CI/CD** . Настройте параметры для рабочих процессов CI/CD и сохраните их в [тайнах GitHub](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).
* **Пошаговое руководство по [«внутреннему циклу разработки»](https://mitchdenny.com/the-inner-loop/)** — разработчик вносит обновления в пример приложения Luis при работе в ветви разработки, проверяет обновления и создает запрос на вытягивание, чтобы предложить изменения и найти утверждение проверки.
* **Выполнение рабочих процессов CI/CD** . выполнение [рабочих процессов непрерывной интеграции для создания и тестирования приложения Luis](luis-concept-devops-automation.md) с помощью действий GitHub.
* **Выполнение автоматического тестирования** . выполните [автоматическое пакетное тестирование для приложения Luis](luis-concept-devops-testing.md) , чтобы оценить качество приложения.
* **Разверните приложение Luis** — выполните [Задание непрерывной поставки (CD)](luis-concept-devops-automation.md#continuous-delivery-cd) , чтобы опубликовать приложение Luis.
* **Использование репозитория с собственным проектом** — объясняется, как использовать репозиторий с собственным приложением Luis.

## <a name="next-steps"></a>Дальнейшие действия

* Используйте [репозиторий шаблонов Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) , чтобы применить DevOps с собственным проектом.
* [Управление исходным кодом и стратегии создания ветвей для LUIS](luis-concept-devops-sourcecontrol.md)
* [Тестирование для LUIS DevOps](luis-concept-devops-testing.md)
* [Рабочие процессы автоматизации для LUIS DevOps](luis-concept-devops-automation.md)
