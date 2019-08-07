---
title: Подключение к устройству Microsoft Azure Шлюз Data Box и управление им с помощью интерфейса Windows PowerShell | Документация Майкрософт
description: Описывает подключение к Шлюз Data Box и управление им с помощью интерфейса Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 650777d849e172686e8b46502a84db8c519174e7
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775194"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Управление устройством Шлюз Azure Data Box с помощью Windows PowerShell

Шлюз Azure Data Box решение позволяет передавать данные по сети в Azure. В этой статье описаны некоторые задачи по настройке и управлению для устройства Шлюз Data Box. Для управления устройством можно использовать портал Azure, локальный веб-интерфейс или интерфейс Windows PowerShell.

В этой статье рассматриваются задачи, выполняемые с помощью интерфейса PowerShell.

Эта статья содержит следующие процедуры.

- Подключение к интерфейсу PowerShell
- Создать пакет поддержки.
- Отправка сертификата
- Загрузка в среде без DHCP
- Просмотр сведений об устройстве

## <a name="connect-to-the-powershell-interface"></a>Подключение к интерфейсу PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Создать пакет поддержки.

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Отправка сертификата

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Загрузка в среде без DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Просмотр сведений об устройстве

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Следующие шаги

- Разверните [Шлюз Azure Data Box](data-box-gateway-deploy-prep.md) на портале Azure.
