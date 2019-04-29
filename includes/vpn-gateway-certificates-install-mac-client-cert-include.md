---
title: включение файла
description: включение файла
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: b214932f43ce20480ee37a7b4edf942f080b38e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680000"
---
При установке сертификата клиента потребуется пароль, созданный при экспорте сертификата клиента.

1. Найдите PFX-файл сертификата и скопируйте его на компьютер Mac. Сертификат для компьютера Mac можно получить разными способами, включая отправку файла сертификата по электронной почте.
2. Дважды щелкните скопированный на компьютер Mac сертификат, чтобы открыть поле **Add Certificates** (Добавить сертификаты). Затем щелкните **Add** (Добавить), чтобы начать установку.

   ![Добавление сертификатов](./media/vpn-gateway-certificates-install-mac-client-cert-include/addcert.png)
3. Введите пароль, созданный при экспорте сертификата клиента. Этот пароль защищает закрытый ключ сертификата. Чтобы завершить регистрацию, нажмите кнопку **ОК**.

   ![Пароль](./media/vpn-gateway-certificates-install-mac-client-cert-include/password.png)