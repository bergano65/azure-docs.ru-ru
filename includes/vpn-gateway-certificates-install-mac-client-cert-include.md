---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b214932f43ce20480ee37a7b4edf942f080b38e8
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444354"
---
При установке сертификата клиента потребуется пароль, созданный при экспорте сертификата клиента.

1. Найдите PFX-файл сертификата и скопируйте его на компьютер Mac. Сертификат для компьютера Mac можно получить разными способами, включая отправку файла сертификата по электронной почте.
2. Дважды щелкните скопированный на компьютер Mac сертификат, чтобы открыть поле **Add Certificates** (Добавить сертификаты). Затем щелкните **Add** (Добавить), чтобы начать установку.

   ![Добавление сертификатов](./media/vpn-gateway-certificates-install-mac-client-cert-include/addcert.png)
3. Введите пароль, созданный при экспорте сертификата клиента. Этот пароль защищает закрытый ключ сертификата. Чтобы завершить регистрацию, нажмите кнопку **ОК**.

   ![Пароль](./media/vpn-gateway-certificates-install-mac-client-cert-include/password.png)