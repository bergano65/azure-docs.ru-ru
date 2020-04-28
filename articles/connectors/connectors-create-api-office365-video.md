---
title: Подключение к видео Office 365
description: Автоматизация задач и рабочих процессов, управляющих видео в Office 365 видео с помощью Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665791"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Управление видео в Office 365 видео с помощью Azure Logic Apps

Подключившись к Office 365 Видео, вы сможете получать информацию об Office 365 Видео, список видео и многое другое. С помощью Office 365 Видео вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Office 365 Видео; 

* использовать действия для проверки состояния видеопортала, получения списка всех видео, имеющихся в канале, и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. 

Например, можно воспользоваться соединителем Поиска Bing для поиска видео в Office 365, а затем соединителем Office 365 Видео для получения сведений об этом видео. Если видео соответствует вашим требованиям, его можно опубликовать в Facebook.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Подключение к Office 365 видео

При добавлении этого соединителя в приложения логики необходимо войти в учетную запись Office 365 Видео и разрешить приложениям логики подключаться к вашей учетной записи.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

После создания подключения укажите свойства Office 365 Видео, такие как имя клиента или идентификатор канала. 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).