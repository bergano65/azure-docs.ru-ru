---
title: Решение Azure VMware от CloudSimple - Распределение общедоступных IP-адресов
description: Описывает, как распределять общедоступные IP-адреса для виртуальных машин в среде Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024302"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Выделение общедоступных IP-адресов для среды Private Cloud

Откройте вкладку публичных IP-адресов на странице Сети для распределения общедоступных IP-адресов для виртуальных машин в среде Private Cloud.

1. [Получите доступ к порталу CloudSimple](access-cloudsimple-portal.md) и выберите **сеть** в боковом меню.
2. Выберите **публичные ИС**.
3. Нажмите **новый общественный IP**.

    ![Страница публичных ИС](media/public-ips-page.png)

4. Введите имя для идентификации записи IP-адреса.
5. Сохраняйте местоположение по умолчанию.
6. Используйте ползунок, чтобы изменить тайм-аут простоя, если это необходимо.
7. Введите локальный IP-адрес, на который требуется присвоить общедоступный IP-адрес.
8. Введите связанное имя DNS.
9. Щелкните элемент **Отправить**.

![Выделение публичных ИС](media/network-public-ip-allocate.png)

Начинается задача выделения общедоступного IP-адреса. Вы можете проверить состояние задачи на странице **«Задачи активности > задачи».** Когда выделение завершено, новая запись отображается на странице публичных IPs.
