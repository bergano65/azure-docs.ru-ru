---
title: Управление протоколами и шифрами в службе управления API Azure | Документация Майкрософт
description: Сведения об управлении протоколами (TLS, SSL) и шифрами (DES) в службе управления API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 91b6cd64a42319b2a5307919c2efe6bc8e5dcd64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657820"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Управление протоколами и шифрами в службе управления API Azure

Служба управления API Azure поддерживает несколько версий протокола TLS для сторон клиента и сервера, а также несколько версий шифра 3DES.

В этом руководстве показано, как управлять конфигурациями протоколов и шифров для экземпляра службы управления API Azure.

![Управление протоколами и шифрами в APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Технические условия

Чтобы выполнить шаги в этой статье, необходимо иметь следующее:

* Экземпляр управления API.

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Как управлять протоколами TLS и шифрами 3DES

1. Перейдите к **экземпляру службы управления API** на портале Azure.
2. Выберите **параметров протокола** в меню.  
3. Включите или отключите нужные протоколы или шифры.
4. Выберите команду **Сохранить**. Изменения будут применены в течение часа.  

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [протоколе TLS](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* См. другие [видео](https://azure.microsoft.com/documentation/videos/index/?services=api-management) об управлении API.