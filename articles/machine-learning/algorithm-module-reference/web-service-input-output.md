---
title: Вход/выход веб-сервиса
description: Узнайте больше о модулях веб-сервисов в дизайнере машинного обучения Azure (предварительный просмотр)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462091"
---
# <a name="web-service-inputoutput"></a>Вход/выход веб-сервиса

В этой статье описывается модуль **ввода Web Service** и модуль **web Service Output** в дизайнере машинного обучения Azure (предварительный просмотр).

Модуль **ввода Web Service** может подключаться к порту ввода только с типом **DataFrameDirectory.** А модуль **Web Service Output** может быть подключен только из выходного порта с типом **DataFrameDirectory.** Эти два модуля можно найти в дереве модуля, под категорией **Web Service.** 

Модуль **ввода Web Service** используется для указания того, где пользовательские данные входят в конвейер, а модуль **Web Service Output** используется для указания того, куда возвращаются пользовательские данные в конвейере вывода в реальном времени.

## <a name="how-to-use-web-service-inputoutput"></a>Как использовать ввод/выход web service

- При создании конвейера вывода в реальном времени из конвейера обучения модуль **Web Service Input** и **Web Service Output** output будут автоматически добавлены, чтобы показать, где пользовательские данные попадают в конвейер и куда возвращаются данные. 

    Подробнее о [создании конвейера выводов в реальном времени.](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)

    > [!NOTE]
    > Автоматический генерация конвейера выводов в реальном времени — это процесс наилучших усилий, основанный на правилах, нет никакой гарантии правильности. В соответствии с требованиями можно вручную добавлять или удалять модули **ввода/вывода Web Service.** Убедитесь, что в конвейере вывода вывода в реальном времени есть хотя бы один модуль **ввода Web Service** и один модуль **Web Service Output.** Если у вас есть несколько модулей **web Service Input** или **Web Service Output,** убедитесь, что они имеют уникальные имена, которые можно ввести имя в правую панель модуля.

- Кроме того, можно вручную создать конвейер вывода в реальном времени, добавив **модули web Service Input** и **Web Service Output** в свой непредставленный конвейер.

    > [!NOTE]
    >  Тип трубопровода будет определен при первом представлении. Поэтому не забудьте добавить web **Service Input** и модуль **Web Service Output,** прежде чем отправлять в первый раз, если вы хотите создать конвейер вывода в реальном времени.

   Ниже приведено, как вручную создавать конвейер выводов в реальном времени из модуля **«Execute Python Script».** 

   ![Пример](media/module/web-service-input-output-example.png)
   
   После отправки конвейера и успешного завершения выполнения вы сможете развернуть конечную точку в реальном времени.
   
   > [!NOTE]
   >  В приведенном выше примере **Enter Data Вручную** обеспечивает схему данных для ввода веб-службы и необходим для развертывания точки в реальном времени. Как правило, необходимо всегда подключать модуль или набор данных к порту, который подключен **Web Service Input** для обеспечения схемы данных.
   
## <a name="next-steps"></a>Следующие шаги
Подробнее о [развертывании конечных точек в реальном времени.](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)
Ознакомьтесь с [набором модулей, доступных](module-reference.md) для машинного обучения Azure.