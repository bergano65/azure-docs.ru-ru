---
title: 'Настройка VPN-клиента для P2S VPN-подключений: Аутентификация Azure AD | Документация Майкрософт'
description: VPN-подключение P2S можно использовать для подключения к виртуальной сети с помощью проверки подлинности Azure AD.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: cherylmc
ms.openlocfilehash: 704dcd6335766a6058de4e520b8dcbca0d304c9d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721471"
---
# <a name="configure-a-vpn-client-for-p2s-vpn-connections-azure-ad-authentication-preview"></a>Настройка VPN-клиента для P2S VPN-подключений: проверка подлинности Azure AD (Предварительная версия)

Эта статья поможет настроить VPN-клиент для подключения к виртуальной сети с помощью VPN типа "точка — сеть" и Azure Active Directory проверки подлинности. Прежде чем можно будет подключиться и пройти проверку подлинности с помощью Azure AD, необходимо сначала настроить клиент Azure AD. Дополнительные сведения см. в статье [Настройка клиента Azure AD](openvpn-azure-ad-tenant.md).

## <a name="profile"></a>Работа с профилями клиентов

Для подключения необходимо [скачать](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) VPN-клиент Azure и настроить профиль VPN-клиента на каждом компьютере, который хочет подключиться к виртуальной сети. Можно создать клиентский профиль на компьютере, экспортировать его, а затем импортировать на дополнительные компьютеры.

### <a name="cert"></a>Создание профиля клиента на основе сертификата

При работе с профилем на основе сертификата убедитесь, что на клиентском компьютере установлены соответствующие сертификаты. Дополнительные сведения о сертификатах см. в разделе [Установка сертификатов клиента](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Создание профиля клиента RADIUS

  ![RADIUS](./media/openvpn-azure-ad-client/create/create-radius1.jpg)

### <a name="export"></a>Экспорт и распространение клиентского профиля

Если у вас есть рабочий профиль и его необходимо распространить другим пользователям, его можно экспортировать, выполнив следующие действия.

1. Выделите профиль VPN-клиента, который требуется экспортировать, щелкните.. **.** , а затем выберите **Экспорт**.

    ![Программе](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Выберите расположение, в которое нужно сохранить этот профиль, оставьте имя файла, а затем нажмите кнопку **сохранить** , чтобы сохранить XML-файл.

    ![Программе](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Импорт профиля клиента

1. На странице выберите **Импорт**.

    ![импорт](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Перейдите к XML-файлу профиля и выберите его. Выбрав файл, нажмите кнопку **Открыть**.

    ![импорт](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Укажите имя профиля и нажмите кнопку **сохранить**.

    ![импорт](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Выберите **Подключиться** , чтобы подключиться к VPN.

    ![импорт](./media/openvpn-azure-ad-client/import/import4.jpg)

5. После подключения значок станет зеленым и будет иметь значение **подключено**.

    ![импорт](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Удаление клиентского профиля

1. Щелкните многоточие рядом с удаляемым клиентским профилем. Затем выберите **Удалить**.

    ![удалить](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Выберите **Удалить** для удаления.

    ![удалить](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Создание подключения

1. На странице выберите **+** , а затем **+ Добавить**.

    ![connection;](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Заполните сведения о подключении. Если вы не уверены в этих значениях, обратитесь к администратору. После заполнения значений выберите **сохранить**.

    ![connection;](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Выберите **Подключиться** , чтобы подключиться к VPN.

    ![connection;](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Выберите правильные учетные данные, а затем нажмите кнопку **продолжить**.

    ![connection;](./media/openvpn-azure-ad-client/create/create4.jpg)

5. После успешного подключения значок будет зеленым и, например, **подключенным**.

    ![connection;](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Автоматическое подключение

Эти действия помогут настроить подключение для автоматического подключения с параметром Always on.

1. На домашней странице VPN-клиента выберите **параметры VPN**.

    ![авто](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Выберите **Да** в диалоговом окне переключение приложений.

    ![авто](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Убедитесь, что подключение, которое вы хотите установить, еще не подключено, затем выделите профиль и установите флажок " **автоматически устанавливать подключение** ".

    ![авто](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Выберите **Подключиться** , чтобы инициировать VPN-подключение.

    ![авто](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Диагностика проблем с подключением

1. Для диагностики проблем с подключением можно использовать средство **диагностики** . Щелкните **...** рядом с VPN-подключением, которое нужно диагностировать, чтобы открыть меню. Затем выберите **Диагностика**.

    ![обнаруживать](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. На странице **Свойства соединения** выберите **запустить диагностику**.

    ![обнаруживать](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Войдите с помощью своих учетных данных.

    ![обнаруживать](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Просмотр результатов диагностики.

    ![обнаруживать](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Создание клиента Azure Active Directory для P2S открытых VPN-подключений, использующих проверку подлинности Azure AD](openvpn-azure-ad-tenant.md).
