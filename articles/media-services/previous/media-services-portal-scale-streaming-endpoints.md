---
title: Масштабирование конечных точек потоковой передачи с помощью портала Azure | Документация Майкрософт
description: В этом учебнике пошагово описано, как масштабировать конечные точки потоковой передачи с помощью портала Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 814d6b55e5ae04bf27dfb70d4e7b5ea2d5a4315c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266822"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Масштабирование конечных точек потоковой передачи с помощью портала Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Обзор

> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Конечные точки уровня **Премиум** подходят для выполнения более сложных задач благодаря выделенной пропускной способности и возможности масштабирования. Клиенты, имеющие конечную точку потоковой передачи уровня **Премиум**, по умолчанию получают одну единицу потоковой передачи. Конечную точку потоковой передачи можно масштабировать, добавляя единицы потоковой передачи. Каждая единица потоковой передачи предоставляет дополнительную пропускную способность для приложения. Дополнительные сведения о типах конечных точек потоковой передачи и конфигурации сети CDN см. в статье [Управление конечными точками потоковой передачи с помощью портала Azure](media-services-streaming-endpoints-overview.md).
 
В этом разделе показано, как масштабировать конечную точку потоковой передачи.

Дополнительные сведения о ценах на службы мультимедиа см. [здесь](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Масштабирование конечных точек потоковой передачи

Чтобы изменить число единиц потоковой передачи, сделайте следующее.

1. Выберите учетную запись служб мультимедиа Azure на [портале Azure](https://portal.azure.com/).
2. В окне **Параметры** щелкните**Конечные точки потоковой передачи**.
3. Щелкните конечную точку потоковой передачи, которую требуется масштабировать. 

    > [!NOTE] 
    > Масштабировать можно только конечные точки потоковой передачи уровня **Премиум**.

4. Переместите ползунок, чтобы указать нужное число единиц потоковой передачи.

    ![конечная точка потоковой передачи](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

