---
title: Шаг 1. Создание рабочей области
titleSuffix: Azure Machine Learning Studio
description: Пошаговое руководство по разработке решения прогнозирования (шаг 1). Узнайте, как настроить новую рабочую область Студии машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: garyericson
ms.author: garye
ms.custom: seodec18
ms.date: 03/23/2017
ms.openlocfilehash: c018988eb36e395372b3d927732a1acbcb5c2fa9
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509518"
---
# <a name="walkthrough-step-1-create-an-azure-machine-learning-studio-workspace"></a>Шаг 1. Создание рабочей области Студии машинного обучения Azure.
Это первый этап из пошагового руководства [Разработка решения для прогнозной аналитики в службе машинного обучения Azure](walkthrough-develop-predictive-solution.md).

1. **Создание рабочей области машинного обучения**
2. [Отправка существующих данных](walkthrough-2-upload-data.md)
3. [Создание нового эксперимента](walkthrough-3-create-new-experiment.md)
4. [Обучение и анализ моделей](walkthrough-4-train-and-evaluate-models.md)
5. [Развертывание веб-службы](walkthrough-5-publish-web-service.md)
6. [Доступ к веб-службе](walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Для использования студии машинного обучения требуется рабочая область машинного обучения Microsoft Azure. Такая рабочая область содержит инструменты, необходимые для создания, публикации экспериментов и управления ими.  

Администратору подписки Azure нужно создать рабочую область и добавить вас в качестве владельца или участника. Подробные сведения см. в статье [Создание рабочей области машинного обучения Azure и предоставление к ней общего доступа](create-workspace.md).

Создав рабочую область, откройте Студию машинного обучения ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Если у вас несколько рабочих областей, можно выбрать рабочую область на панели инструментов в правом верхнем углу окна.

![Выбор рабочей области в Студии][2]

> [!TIP]
> Владелец рабочей области может предоставить общий доступ к экспериментам, над которыми он работает, пригласив других пользователей в свою рабочую область. Это можно сделать в Студии машинного обучения на странице **ПАРАМЕТРЫ** . Для каждого пользователя необходима учетная запись Майкрософт или учетная запись организации.
> 
> На странице **Параметры** щелкните **Пользователи**, а затем — **Invite more users** (Пригласить пользователей) в нижней части окна.
> 
> 

- - -
**Дальнейшие действия. [Передача существующих данных](walkthrough-2-upload-data.md)**

[1]: ./media/walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/walkthrough-1-create-ml-workspace/open-workspace.png
