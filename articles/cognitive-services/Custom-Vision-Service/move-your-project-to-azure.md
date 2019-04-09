---
title: Переместить ограниченного пробного проекта в Azure
titlesuffix: Azure Cognitive Services
description: Узнайте, как переместить проект ограниченной пробной версии в Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056883"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Как переместить проект ограниченной пробной версии в Azure

Пользовательская служба визуального распознавания по завершении его перемещения в Azure, заканчивается поддержка проектов ограниченной пробной версии за пределами Azure. В этом документе будет показано, как с помощью API компьютерного зрения Custom Копировать ограниченной пробной версии проекта к ресурсу Azure.

Поддержка просмотра проекты ограниченной пробной версии на [концепции пользовательских веб-сайт](https://customvision.ai) закончилась 25 марта 2019 г. В этом документе теперь показано, как использовать пользовательские интерфейсы API компьютерного зрения с [скрипт python миграции](https://github.com/Azure-Samples/custom-vision-move-project) на GitHub) дублирование проекта к ресурсу Azure.

Дополнительные сведения, включая крайние сроки ключа в процессе ограниченного устаревания пробной версии, вы найдете [заметки о выпуске](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) или к связям по электронной почте владельцам ограниченной пробной версии проектов.

[Скрипт переноса](https://github.com/Azure-Samples/custom-vision-move-project) позволяет повторно создать проект, загрузки, а затем загружает все теги, области и образы в текущей итерации. Она обеспечит переход с новым проектом в новой подписки и затем вы можете обучить.

## <a name="prerequisites"></a>Технические условия

- Вам потребуется действительная подписка Azure, связанные с учетной записью Майкрософт или учетную запись Azure Active Directory (AAD), которую необходимо использовать для входа на [концепции пользовательских веб-сайт](https://customvision.ai). 
    - Если у вас нет учетной записи Azure [создать учетную запись](https://azure.microsoft.com/free/) бесплатно.
    - Введение в основные понятия Azure подписок и ресурсов, см. в разделе [руководством для разработчиков Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [PIP](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Создание пользовательских концепции ресурсов на портале Azure

Использование пользовательской службы визуального распознавания в Azure, необходимо создать ресурсы Custom Vision обучения и прогнозирования в [портала Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Несколько проектов может быть связан один ресурс. Более подробные сведения о [цены и ограничения](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) доступен. Чтобы продолжить использование пользовательской службы визуального распознавания, бесплатно, можно выбрать уровень F0 на портале Azure. 

> [!NOTE]
> При перемещении Custom Vision проект к ресурсу Azure, он наследует базовый [разрешения]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) этого ресурса Azure. Если владельцы ресурса Azure, проект другим пользователям в вашей организации, они смогут для доступа к проекту на [концепции пользовательских веб-сайт](https://customvision.ai). Аналогично при удалении ресурсов будут удалены проектов.  

## <a name="find-your-limited-trial-project-information"></a>Поиск сведений о проекте ограниченной пробной версии

Чтобы переместить проект, необходимо будет _Идентификатором проекта_ и _обучения ключ_ для проекта, который вы пытаетесь перенести. Если у вас эти сведения, посетите [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) Чтобы получить идентификатор и ключ для каждого проекта. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Для копирования проекта в Azure с помощью образца кода Python

Выполните [примеры инструкций кода](https://github.com/Azure-Samples/custom-vision-move-project), с помощью ограниченной пробной версии ключа и Идентификатором проекта как материалы «источник», а также ключ, в новый ресурс Azure, созданный в качестве «назначения».

По умолчанию все проекты ограниченной пробной версии, размещенные в Южной центральном регионе США Azure.

## <a name="next-steps"></a>Дальнейшие действия

Теперь проект перемещен к ресурсу Azure. Необходимо будет обновить ключи во всех приложениях, которые вы написали обучения и прогнозирования.

Для просмотра проекта на [концепции пользовательских веб-сайт](https://customvision.ai), войдите с помощью одной учетной записи, использованной для входа на портал Azure. Если вы не видите проекта, убедитесь, что, в том же каталоге, в [концепции пользовательских веб-сайт](https://customvision.ai) как каталог, где находятся ресурсы на портале Azure. В портале Azure и CustomVision.ai можно выбрать каталог из раскрывающегося меню пользователя в правом верхнем углу экрана.