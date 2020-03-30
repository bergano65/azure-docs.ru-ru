---
title: Решение Azure VMware от CloudSimple - Налаживает VPN между наймом и частным облаком
description: Описывает, как настроить VPN-соединение от сайта к сайту или точке к сайту между вашей предварительной сетью и вашим частным облаком CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087127"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Налажить VPN соединение с вашим облачным частным облаком

VPN шлюзы позволяют подключиться к сети CloudSimple из вашей клиентской сети и с клиентского компьютера удаленно.  В этой статье вы можете найти информацию о настройке VPN шлюзов с портала CloudSimple.  VPN-соединение между вашей предварительной сетью и сетью CloudSimple обеспечивает доступ к vCenter и рабочим нагрузкам в вашем частном облаке. CloudSimple поддерживает как VPN-сайт, так и VPN-шлюзы от сайта к сайту.

## <a name="vpn-gateway-types"></a>Типы VPN-шлюзов

* **VPN-соединение от точки к сайту** — это самый простой способ подключения к частному облаку с компьютера. Используйте VPN-соединение от точки к сайту для удаленного подключения к частному облаку.
* **VPN-соединение от сайта к сайту** позволяет настроить рабочие нагрузки Private Cloud для доступа к службам. Вы также можете использовать в предварительном Active Directory в качестве источника идентификации для проверки подлинности вашего Private Cloud vCenter.  В настоящее время поддерживается тип **VPN на основе политики.**

В регионе можно создать один VPN шлюз от сайта к сайту и один VPN-шлюз от точки к сайту.

## <a name="point-to-site-vpn"></a>VPN-подключение типа "точка — сеть"

Чтобы создать VPN шлюз от точки к сайту, [см.](vpn-gateway.md#create-point-to-site-vpn-gateway)

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Подключитесь к CloudSimple с помощью VPN от точки к сайту

VPN клиент необходим для подключения к CloudSimple с вашего компьютера.  Скачать [клиент OpenVPN](https://openvpn.net/community-downloads/) для Windows или [Viscosity](https://www.sparklabs.com/viscosity/download/) для macOS и OS X.

1. Запустите портал CloudSimple и выберите **сеть**.
2. Выберите **VPN шлюз**.
3. Из списка VPN шлюзов нажмите на VPN шлюз Point-to-Site.
4. Выберите **пользователей**.
5. Нажмите на **Скачать мою конфигурацию VPN**

    ![Загрузка конфигурации VPN](media/download-p2s-vpn-configuration.png)

6. Импортировать конфигурацию в клиенте VPN

    * Инструкции по [импорту конфигурации на клиенте Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Инструкции по [импорту конфигурации на macOS или OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Подключитесь к VPN-шлюзу CloudSimple.

Пример ниже показывает импорт соединения с помощью **Viscosity Client**.

#### <a name="import-connection-on-viscosity-client"></a>Импортное соединение на клиенте Viscosity

1. Извлеките содержимое конфигурации VPN из загруженного файла .zip.

2. Откройте viscosity на вашем компьютере.

3. Нажмите **+** значок и выберите **соединение** > импорта**из файла.**

    ![Конфигурация импортного VPN из файла](media/import-p2s-vpn-config.png)

4. Выберите файл конфигурации OpenVPN (.ovpn) для протокола, который вы хотите использовать, и нажмите **Open.**

    ![Виртуальная частная сеть](media/import-p2s-vpn-config-choose-ovpn.png)

Теперь соединение отображается в меню Viscosity.

#### <a name="connect-to-the-vpn"></a>Подключитесь к VPN.

Чтобы подключиться к VPN с помощью клиента Viscosity OpenVPN, выберите соединение из меню. Значок меню обновляется, чтобы указать, что соединение установлено.

![Виртуальная частная сеть](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Подключение к нескольким частным облакам

VPN-соединение «Точка к сайту» разрешает DNS-имена первого частного облака, которое вы создаете. Если вы хотите получить доступ к другим частным облакам, вы должны обновить DNS-сервер на вашем клиенте VPN.

1. Запуск [cloudSimple портала](access-cloudsimple-portal.md).

2. Перейдите к**частным облакам** **ресурсов** > и выберите частное облако, к который вы хотите подключиться.

3. На **странице Private** Cloud скопируйте IP-адрес сервера Private Cloud DNS в **базовой информации.**

    ![Частные облачные DNS-серверы](media/private-cloud-dns-server.png)

4. Нажмите справа на значок Viscosity в лоток системы компьютера и выберите **Предпочтения.**

    ![Виртуальная частная сеть](media/vis00.png)

5. Выберите VPN-соединение CloudSimple.

    ![Подключение VPN](media/viscosity-client.png)

6. Нажмите **"Изменить",** чтобы изменить свойства подключения.

    ![Отодытная связь VPN](media/viscosity-edit-connection.png)

7. Нажмите на вкладку **Сети** и введите IP-адреса сервера Private ```cloudsimple.io```Cloud DNS, разделенные запятой или пространством и доменом как.  Выберите **параметры Ignore DNS, отправленные VPN-сервером.**

    ![VPN Сети](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Чтобы подключиться к первому частному облаку, удалите эти настройки и подключитесь к VPN-серверу.

## <a name="site-to-site-vpn"></a>VPN-подключение "сеть-сеть"

Чтобы создать VPN шлюз от сайта к сайту, [см.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)  VPN-соединение от сайта к сайту из вашей сети в Private Cloud обеспечивает эти преимущества.  

* Доступность вашего Private Cloud vCenter с любой рабочей станции в вашей предварительной сети
* Использование вашего наместератого Active Directory в качестве источника идентификации vCenter
* Удобная передача шаблонов VM, II и других файлов из ваших закрытых ресурсов в ваш Private Cloud vCenter
* Доступность рабочих нагрузок, работающих в частном облаке, из вашей предварительной сети

Чтобы настроить свой vpn шлюз в режиме высокой доступности, [см.](high-availability-vpn-connection.md)

> [!IMPORTANT]
>    1. Установите TCP MSS Clamping на 1200 на вашем устройстве VPN. Или, если ваши VPN устройства не поддерживают MSS зажима, вы можете в качестве альтернативы установить MTU на туннель интерфейс е1240 байтов вместо.
> 2. После настройки VPN от сайта к сайту, переадрестировать запросы DNS на cloudsimple.io на серверы Private Cloud DNS.  Следуйте инструкциям в [On-Premises DNS Setup](on-premises-dns-setup.md).
