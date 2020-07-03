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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485678"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Создайте клиент Azure AD.

Создайте клиент Azure AD, выполнив действия, описанные в статье [Создание нового клиента](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Название организации
* Первоначальное доменное имя

  Пример.

   ![Новый клиент Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Создание пользователей клиента

На этом шаге вы создадите двух пользователей клиента Azure AD: одну учетную запись глобального администратора и одну учетную запись главного пользователя. Учетная запись главного пользователя используется как учетная запись главного внедрения (учетная запись службы). При создании учетной записи пользователя клиента Azure AD необходимо настроить роль каталога для типа пользователя, который требуется создать. Выполните действия, описанные в [этой статье](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) , чтобы создать по крайней мере двух пользователей для вашего клиента Azure AD. Не забудьте изменить **роль каталога** , чтобы создать типы учетных записей:

* глобальный администратор.
* User (Пользователь)

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Регистрация VPN-клиента

Зарегистрируйте VPN-клиент в клиенте Azure AD.

1. Укажите идентификатор каталога, который вы хотите использовать для проверки подлинности. Он указан в разделе Свойства страницы Active Directory.

    ![Идентификатор каталога](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Скопируйте идентификатор каталога.

3. Войдите в портал Azure в качестве пользователя, которому назначена роль **глобального администратора** .

4. Затем предоставьте администратору согласие. Скопируйте и вставьте URL-адрес, относящийся к расположению развертывания, в адресной строке браузера:

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

5. При появлении запроса выберите учетную запись **глобального администратора** .

    ![Идентификатор каталога](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. При появлении запроса выберите **принять** .

    ![Принять](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. В Azure AD в разделе **корпоративные приложения**вы увидите список **VPN Azure** .

     ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. регистрация дополнительных приложений

На этом шаге вы зарегистрируете дополнительные приложения для различных пользователей и групп.

1. В Azure Active Directory щелкните **Регистрация приложений** а затем **+ Новая регистрация**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. На странице **Регистрация приложения** введите **имя**. Выберите требуемые **Поддерживаемые типы учетных записей**и нажмите кнопку **зарегистрировать**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. После регистрации нового приложения щелкните **открыть API** в колонке приложения.

4. Щелкните **+ Добавить область**.

5. Оставьте **URI идентификатора приложения**по умолчанию. Щелкните **Сохранить и продолжить**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Заполните обязательные поля и убедитесь, что **состояние** **включено**. Нажмите кнопку **Добавить область**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Щелкните **открыть API** , а затем — **добавить клиентское приложение**.  В поле **идентификатор клиента**введите следующие значения в зависимости от облака.

    - Введите **41b23e61-6c1e-4545-b367-cd054e0ed4b4** для **общедоступной** службы Azure
    - Введите **51bb15d4-3a4f-4ebf-9dca-40096fe32426** для Azure для **государственных организаций**
    - Введите **538ee9e6-310A-468d-Afef-ea97365856a9** для Azure для **Германии**
    - Ввод **49f817b6-84ae-4cc0-928c-73f27289b3aa** для Azure для **Китая**

8. Нажмите **Добавить приложение**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Скопируйте **идентификатор приложения (Client)** на странице **обзора** . Эти сведения понадобятся вам для настройки VPN-шлюзов.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Повторите действия, описанные в разделе [Регистрация дополнительных приложений](#register-apps) , чтобы создать столько приложений, сколько требуется для требований безопасности. Каждое приложение будет связано с VPN-шлюзом и может иметь другой набор пользователей. К шлюзу может быть привязано только одно приложение.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Назначение пользователей для приложений

Назначьте пользователей приложениям.

1. В разделе **Azure AD — > корпоративные приложения**выберите только что зарегистрированное приложение и щелкните **свойства**. Убедитесь, что **требуется назначение пользователей?** значение **Да**. Выберите команду **Сохранить**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. На странице приложение выберите **Пользователи и группы**, а затем щелкните **+ Добавить пользователя**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. В разделе **Добавление назначения**щелкните **Пользователи и группы**. Выберите пользователей, которым будет предоставлен доступ к этому приложению VPN. Нажмите кнопку **Выбрать**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)