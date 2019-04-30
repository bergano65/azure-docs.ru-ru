---
title: Памятка по конвейеру данных машинного обучения Azure — командный процесс обработки и анализа данных
description: Пригодная для печати памятка по настройке автоматического конвейера данных в веб-службу машинного обучения Azure в зависимости от того, хранятся ли данные на локальном компьютере, транслируются в режиме реального времени или находятся в Azure или в независимой облачной службе.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/14/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=garyericson, previous-ms.author=garye
ms.openlocfilehash: 1245255e65412f9bbcba3c8d9caa76b9ffd2dcc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826208"
---
# <a name="cheat-sheet-for-an-automated-data-pipeline-for-azure-machine-learning-predictions"></a>Памятка по работе с автоматическим конвейером данных для создания прогнозов машинного обучения Azure
**Памятка по работе с автоматическим конвейером данных машинного обучения Microsoft Azure** поможет вам разобраться в технологии, которую вы можете использовать для передачи данных в веб-службу машинного обучения и их последующей оценки с помощью модели прогнозной аналитики.

Выбор способа перемещения данных в конечную точку веб-службы для последующей оценки зависит от того, хранятся ли ваши данные на локальном компьютере, находятся в облаке или транслируются в режиме реального времени.
Это памятка рассказывает, какие решения необходимо принять, и содержит ссылки на статьи, которые могут помочь вам в разработке решения.

## <a name="download-the-machine-learning-automated-data-pipeline-cheat-sheet"></a>Загрузить памятку по работе с автоматическим конвейером данных машинного обучения
Загрузив памятку, распечатайте ее в формате А3 (27,94 x 43,18 см).

Загрузить памятку можно здесь: **[Памятка по работе с автоматическим конвейером данных Машинного обучения Microsoft Azure](https://download.microsoft.com/download/C/C/7/CC726F8B-2E6F-4C20-9B6F-AFBEE8253023/microsoft-machine-learning-operationalization-cheat-sheet_v1.pdf)**

![Обзор возможностей Студии машинного обучения Microsoft Azure][op-cheat-sheet]

[op-cheat-sheet]: ./media/automated-data-pipeline-cheat-sheet/machine-learning-automated-data-pipeline-cheat-sheet_v1.1.png


## <a name="more-help-with-machine-learning-studio"></a>Дополнительная справка по Студии машинного обучения
* Обзор машинного обучения Microsoft Azure вы можете найти в разделе [Введение в машинное обучение в Microsoft Azure](../studio/what-is-machine-learning.md).
* Инструкции по развертыванию веб-службы оценки см. в статье [Развертывание веб-службы машинного обучения Azure](../studio/publish-a-machine-learning-web-service.md).
* Обсуждение работы с веб-службой оценки см. в статье [Как использовать веб-службу машинного обучения Azure](../studio/consume-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

