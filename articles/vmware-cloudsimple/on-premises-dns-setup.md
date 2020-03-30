---
title: Решение Azure VMware от CloudSimple - налаживание DNS для облачного частного облака
description: Описывает, как настроить разрешение dNS-имен для доступа к серверу vCenter на облачном частном облаке с закрытых рабочих станций
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246114"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Настройка DNS для разрешения имен для доступа Private Cloud vCenter с закрытых рабочих станций

Чтобы получить доступ к серверу vCenter в облачном частном облаке с закрытых рабочих станций, необходимо настроить разрешение DNS-адреса, чтобы сервер vCenter мог быть адресован как по имени хоста, так и по IP-адресу.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Получение IP-адреса DNS-сервера для вашего частного облака

1. Войти на [портал CloudSimple](access-cloudsimple-portal.md).

2. Перейдите к**частным облакам** **ресурсов** > и выберите частное облако, к который вы хотите подключиться.

3. На **странице Private** Cloud under **Basic Info**скопите IP-адрес сервера Private Cloud DNS.

    ![Частные облачные DNS-серверы](media/private-cloud-dns-server.png)


Используйте любой из этих параметров для конфигурации DNS.

* [Создайте зону на сервере DNS для cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Создайте условный анкер на локальном DNS-сервере для решения решения cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Создайте зону на сервере DNS для cloudsimple.io

Вы можете настроить зону в качестве заглушки и указать на DNS-серверы в частном облаке для разрешения имен. В этом разделе содержится информация об использовании сервера BIND DNS или DNS-сервера Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Создание зоны на сервере BIND DNS

Конкретный файл и параметры для настройки могут варьироваться в зависимости от вашей индивидуальной настройки DNS.

Например, для конфигурации сервера BIND по умолчанию отодевайте /etc/named.conf файл на вашем сервере DNS и добавьте следующую информацию о зоне.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Создание зоны на dNS-сервере Microsoft Windows

1. Нажмите правой кнопкой мыши на сервере DNS и выберите **новую зону.** 
  
    ![Новая зона](media/DNS01.png)
2. Выберите **заглушку зоны** и нажмите **далее**.

    ![Новая зона](media/DNS02.png)
3. Выберите подходящий вариант в зависимости от среды и нажмите **далее.**

    ![Новая зона](media/DNS03.png)
4. Выберите **зону поиска вперед** и нажмите **далее**.

    ![Новая зона](media/DNS01.png)
5. Введите название зоны и нажмите **далее**.

    ![Новая зона](media/DNS05.png)
6. Введите IP-адреса DNS-серверов для вашего частного облака, полученные на портале CloudSimple.

    ![Новая зона](media/DNS06.png)
7. Нажмите **далее** по мере необходимости для завершения установки мастера.

## <a name="create-a-conditional-forwarder"></a>Создание условного форварда

Условный форвард перенаправляет все запросы разрешения dNS на указанный сервер. С этой настройкой любой запрос на cloudsimple.io на DNS-серверы, расположенные в частном облаке. Ниже приведены следующие примеры, как настроить переадресов на различных типах DNS-серверов.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Создание условного антолера на сервере BIND DNS

Конкретный файл и параметры для настройки могут варьироваться в зависимости от вашей индивидуальной настройки DNS.

Например, для конфигурации сервера BIND по умолчанию отодевайте /etc/named.conf файл на вашем сервере DNS и добавьте следующую условную информацию о пересылке.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Создание условного анкера на dNS-сервере Microsoft Windows

1. Откройте DNS-менеджер на сервере DNS.
2. Нажмите правой кнопкой **Условные форварды** и выберите опцию для добавления нового условного форварда.

    ![Условный форвард 1 Windows DNS](media/DNS08.png)
3. Введите домен DNS и IP-адрес DNS-серверов в приватном облаке и **нажмите OK**.
