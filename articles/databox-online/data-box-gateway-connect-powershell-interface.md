---
title: Соединиться с устройства и управление им шлюз поле данных Microsoft Azure через интерфейс Windows PowerShell | Документация Майкрософт
description: Описывает, как подключиться к и затем управлять шлюз поле данных через интерфейс Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 0ca570235ac2a87b62c5d0fcebbd24dc5186e37d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556516"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Управление устройством шлюза поле данных Azure с помощью Windows PowerShell

Решение Azure шлюз поле данных позволяет отправлять данные по сети в Azure. В этой статье описываются некоторые задачи конфигурации и управления для устройства шлюза поле данных. Можно использовать портал Azure, локального веб-интерфейса или в интерфейсе Windows PowerShell для управления устройством.

Эта статья посвящена задачи, которые можно сделать с помощью интерфейса PowerShell.

В этой статье описываются следующие процедуры:

- Подключение к интерфейсу PowerShell
- Запуск сеанса поддержки
- Создать пакет поддержки.
- Передача сертификата
- Загружается в среде без DHCP
- Просмотр сведений об устройстве

> [!IMPORTANT]
> Шлюз Azure поле данных находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connect-to-the-powershell-interface"></a>Подключение к интерфейсу PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>Запуск сеанса поддержки

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>Создать пакет поддержки.

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Передача сертификата

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Загружается в среде без DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Просмотр сведений об устройстве

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Разверните [Шлюз Azure Data Box](data-box-gateway-deploy-prep.md) на портале Azure.
