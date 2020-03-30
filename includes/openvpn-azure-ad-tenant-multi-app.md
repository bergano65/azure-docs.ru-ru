---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485678"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Создание арендатора Azure AD

Создайте aD-арендатора Azure, используя шаги в новой статье [для арендатора:](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Организационное название
* Первоначальное доменное имя

  Пример

   ![Новый арендатор Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Создание пользователей-арендаторов

На этом этапе вы создаете двух пользователей-арендаторов Azure AD: учетную запись «Один глобальный админ» и одну учетную запись пользователя. Главная учетная запись пользователя используется в качестве основной учетной записи встраивания (учетная запись обслуживания). При создании учетной записи пользователя-арендатора Azure AD можно настроить роль каталога для типа пользователя, который вы хотите создать. Используйте шаги в [этой статье,](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) чтобы создать по крайней мере двух пользователей для арендатора Azure AD. Обязательно измените **роль каталога** для создания типов учетных записей:

* глобальный администратор.
* Пользователь

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Зарегистрируйте VPN Клиента

Зарегистрируйте VPN-клиент в клиенте Azure AD.

1. Найдите идентификатор каталога, который вы хотите использовать для проверки подлинности. Он указан в разделе свойств страницы Active Directory.

    ![Идентификатор каталога](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Скопируйте идентификатор каталога.

3. Вопиюсь на портал Azure в качестве пользователя, которым назначена роль **глобального администратора.**

4. Далее, дать согласие админа. Копируйте и вставьте URL-адрес, относящихся к местоположению развертывания в адресной строке браузера:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure для государственных организаций

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Выберите учетную запись **Глобального адиме,** если это вызвано.

    ![Идентификатор каталога](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Выберите **Принять** при запросе.

    ![Принять](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. В Azure AD в **корпоративных приложениях**вы увидите список **VPN Azure.**

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Регистрация дополнительных заявок

На этом этапе вы регистрируете дополнительные приложения для различных пользователей и групп.

1. Под вашим лазурным active Directory щелкните **регистрацию приложений,** а затем **и новую регистрацию.**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. На **странице регистрации введите** **имя**. Выберите желаемые **типы поддерживаемых учетных записей,** затем нажмите **Регистрация**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. После регистрации нового приложения нажмите **Expose API** под лезвием приложения.

4. Нажмите **кнопку и добавьте область.**

5. Оставьте **идентификатор приложения URI.** Щелкните **Сохранить и продолжить**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Заполните необходимые поля и убедитесь, что **состояние** **включено.** Нажмите **Добавить область**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Нажмите **Expose API,** а затем **добавьте клиентское приложение.**  Для **идентификатора клиента**введите следующие значения в зависимости от облака:

    - Введите **41b23e61-6c1e-4545-b367-cd054e0ed4b4** для Azure **Public**
    - Введите **51bb15d4-3a4f-4ebf-9dca-40096fe32426** для **правительства** Лазурного государства
    - Введите **538ee9e-310a-468d-afef-ea97365856a9** для Azure **Германии**
    - Введите **49f817b6-84ae-4cc0-928c-73f27289b3aa** для Azure **Китай 21Vianet**

8. Нажмите **Добавить приложение**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Копирование **идентификатора приложения (клиента)** со страницы **Обзор.** Вам понадобится эта информация для настройки вашего VPN шлюза (ы).

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Повторите шаги в этом [разделе дополнительных приложений регистра,](#register-apps) чтобы создать как можно больше приложений, необходимых для вашего требования безопасности. Каждое приложение будет связано с VPN шлюзом и может иметь другой набор пользователей. Только одно приложение может быть связано с шлюзом.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Назначать пользователей приложениям

Назначайте пользователей в приложения.

1. В **приложениях Azure AD -> Enterprise**выберите недавно зарегистрированное приложение и нажмите **Properties.** Убедитесь, что **назначение пользователя требуется?** установлен **на да**. Нажмите **Сохранить**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. На странице приложения щелкните **пользователей и групп,** а затем нажмите **кнопку «Добавить пользователя**».

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Под **add Assignment**щелкните **пользователей и группы.** Выберите пользователей, которые вы хотите иметь доступ к этому VPN-приложению. Нажмите **Выберите**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)