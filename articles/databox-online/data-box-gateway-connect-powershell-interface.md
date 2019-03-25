---
title: Соединиться с устройства и управление им шлюз поле данных Microsoft Azure через интерфейс Windows PowerShell | Документация Майкрософт
description: Описывает, как подключиться к и затем управлять шлюз поле данных через интерфейс Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403499"
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

## <a name="connect-to-the-powershell-interface"></a>Подключение к интерфейсу PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

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
