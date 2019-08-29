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
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: f7c7fdd06480ce3da70c86d38ab0685b9b3aaaf2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072389"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Управление протоколами и шифрами в службе управления API Azure

Служба управления API Azure поддерживает несколько версий протокола TLS для сторон клиента и сервера, а также несколько версий шифра 3DES.

В этом руководстве показано, как управлять конфигурациями протоколов и шифров для экземпляра службы управления API Azure.

![Управление протоколами и шифрами в APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить шаги в этой статье, необходимо иметь следующее:

* Экземпляр управления API.

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Как управлять протоколами TLS и шифрами 3DES

1. Перейдите к **экземпляру службы управления API** на портале Azure.
2. В меню выберите пункт **параметры протокола** .  
3. Включите или отключите нужные протоколы или шифры.
4. Нажмите кнопку **Сохранить**. Изменения будут применены в течение часа.  

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о [протоколе TLS](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* См. другие [видео](https://azure.microsoft.com/documentation/videos/index/?services=api-management) об управлении API.