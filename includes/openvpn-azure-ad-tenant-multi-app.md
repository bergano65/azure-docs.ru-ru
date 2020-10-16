---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: df3d1c34fe0fc045e829c2ea9166664f06fd3127
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116754"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Создание клиента Azure AD

Создайте клиента Azure AD, выполнив действия, описанные в статье [Создание нового клиента](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Имя организации
* Первоначальное доменное имя

  Пример

   ![Новый клиент Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Создание пользователей клиента

На этом этапе вы создадите двух пользователей клиента Azure AD: одну учетную запись глобального администратора и одну учетную запись главного пользователя. Учетная запись главного пользователя используется как главная учетная запись внедрения (учетная запись службы). При создании учетной записи пользователя клиента Azure AD необходимо настроить роль каталога под тип пользователя, который требуется создать. Выполните действия, описанные в [этой статье](../articles/active-directory/fundamentals/add-users-azure-active-directory.md), чтобы создать по крайней мере двух пользователей для клиента Azure AD. Не забудьте изменить **Роль каталога**, чтобы создать типы учетных записей:

* глобальный администратор.
* Пользователь

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Регистрация VPN-клиента

Зарегистрируйте VPN-клиент в клиенте Azure AD.

1. Укажите идентификатор каталога, который хотите использовать для проверки подлинности. Он указан в разделе свойств страницы Active Directory.

    ![Идентификатор каталога](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Скопируйте идентификатор каталога.

3. Войдите на портал Azure как пользователь с ролью **Глобального администратора**.

4. Затем дайте согласие администратора. Скопируйте и вставьте URL-адрес, относящийся к расположению развертывания, в адресную строку браузера:

    Общедоступные

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure для государственных организаций

    ```
    https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

> [!NOTE]
> Если вы используете учетную запись глобального администратора, которая не является собственной для клиента Azure AD для предоставления согласия, замените "Common" идентификатором каталога Azure AD в URL-адресе. Также может потребоваться заменить «Common» идентификатором каталога в некоторых других случаях.
>

5. При появлении запроса выберите учетную запись **Глобальный администратор**.

    ![Идентификатор каталога](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Выберите **Принять** при появлении соответствующего запроса.

    ![На снимке экрана показано окно с разрешениями на сообщения, запрошенными для вашей организации, и сведениями о запросе.](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. В Azure AD в разделе **Корпоративные приложения** можно будет увидеть в списке **VPN Azure**.

     ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Регистрация дополнительных приложений

На этом шаге вы зарегистрируете дополнительные приложения для различных пользователей и групп.

1. На странице Azure Active Directory выберите **Регистрация приложений**, а затем **+ Новая регистрация**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. На странице **Регистрация приложения** введите **Имя**. Выберите требуемые **Поддерживаемые типы учетных записей**, а затем щелкните **Зарегистрировать**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. После регистрации нового приложения щелкните **Предоставление API** в колонке приложения.

4. Щелкните **+ Добавить область**.

5. Оставьте **URI идентификатора приложения** по умолчанию. Щелкните **Сохранить и продолжить**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Заполните обязательные поля и убедитесь, что **Состояние** — **Включено**. Щелкните **Добавить область**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Щелкните **Предоставление API**, затем **+ Добавить клиентское приложение**.  В поле **Идентификатор клиента** введите следующие значения в зависимости от облака.

    - Введите **41b23e61-6c1e-4545-b367-cd054e0ed4b4** для **общедоступного облака Azure**.
    - Введите **51bb15d4-3a4f-4ebf-9dca-40096fe32426** для **Azure для государственных организаций**.
    - Введите **538ee9e6-310A-468d-Afef-ea97365856a9** для **Microsoft Azure — Германия**.
    - Введите **49f817b6-84ae-4cc0-928c-73f27289b3aa** для **Azure для Китая (21Vianet)** .

8. Нажмите **Добавить приложение**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Скопируйте **Идентификатор приложения (клиента)** со страницы **Обзор**. Эта информация нам понадобится для настройки шлюза (-ов) VPN.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Повторите действия, описанные в этом разделе [регистрации дополнительных приложений](#register-apps), чтобы создать столько приложений, сколько требуется по требованиям безопасности. Каждое приложение будет связано с VPN-шлюзом и может иметь свой набор пользователей. К шлюзу может быть привязано только одно приложение.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Назначение пользователей приложениям

Назначьте пользователей своим приложениям.

1. В разделе **Azure AD -> Корпоративные приложения** выберите только что зарегистрированное приложение и щелкните **Свойства**. Убедитесь, что **Требуется назначение пользователей?** имеет значение **Да**. Выберите команду **Сохранить**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. На странице приложение щелкните **Пользователи и группы**, а затем щелкните **+Добавить пользователя**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. В разделе **Добавление назначения** выберите **Пользователи и группы**. Выберите пользователей, которым будет предоставлен доступ к этому приложению VPN. Нажмите кнопку **Выбрать**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
