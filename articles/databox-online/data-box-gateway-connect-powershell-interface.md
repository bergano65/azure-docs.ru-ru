---
title: Используйте Windows PowerShell для подключения к устройству Azure Data Box Gateway и управления им. Лазурного устройства
description: Описывает, как подключиться к шлюзу Data Box Gateway и затем управлять ими через интерфейс Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260219"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Управление устройством Azure Data Box Gateway с помощью Windows PowerShell

Решение Azure Data Box Gateway позволяет отправлять данные по сети в Azure. В этой статье описаны некоторые задачи конфигурации и управления для устройства Data Box Gateway. Для управления устройством можно использовать портал Azure, локальный веб-интерфейс или интерфейс Windows PowerShell.

В этой статье основное внимание уделяется задачам, которые вы делаете с помощью интерфейса PowerShell.

Эта статья включает в себя следующие процедуры:

- Подключение к интерфейсу PowerShell
- Создать пакет поддержки.
- Передача сертификата
- Загрузка в не-DHCP окружающей среды
- Просмотр информации об устройстве

## <a name="connect-to-the-powershell-interface"></a>Подключение к интерфейсу PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Создать пакет поддержки.

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Передача сертификата

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Загрузка в не-DHCP окружающей среды

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Просмотр информации об устройстве

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Разверните [Шлюз Azure Data Box](data-box-gateway-deploy-prep.md) на портале Azure.
