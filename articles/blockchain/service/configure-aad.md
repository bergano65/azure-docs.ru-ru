---
title: Настройте доступ к активному каталогу Azure - Служба блокчейнAzре
description: Как настроить блокчейн-сервис Azure с доступом к активному каталогу Azure
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455874"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Как настроить доступ к активному каталогу Azure для службы блокчейн Azure

В этой статье вы узнаете, как предоставить доступ и подключиться к узтам службы блокчейн Azure с помощью идентимативных данных пользователя, группы или приложения Azure AD.

Azure AD обеспечивает управление облачными идентификационными данными и позволяет использовать единую идентификацию на всей территории предприятия и доступ к приложениям в Azure. Служба блокчейн Azure интегрирована с Azure AD и предлагает такие преимущества, как федерация идентификационных единого удостоверения личности и многофакторная аутентификация.

## <a name="prerequisites"></a>Предварительные требования

* [Создание участника блокчейна с помощью портала Azure](create-member.md)

## <a name="grant-access"></a>Предоставление доступа

Вы можете предоставить доступ как на уровне участника, так и на уровне узлов. Предоставление прав на доступ на уровне членов, в свою очередь, предоставит доступ ко всем узлам, находивающимся под данным участником.

### <a name="grant-member-level-access"></a>Доступ к члену Гранта

Предоставить разрешение на доступ на уровне участника.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Перейдите к **управлению доступом (IAM) > Добавить > добавить назначение ролей.**
1. Выберите роль **доступа к узловум** блокчейну (Preview) и добавьте объект Azure AD ID, к который вы хотите предоставить доступ. Объектом Azure AD ID может быть:

    | Объект Azure AD | Пример |
    |-----------------|---------|
    | Пользователь Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Группа Azure AD  | `sales@contoso.onmicrosoft.com` |
    | Идентификатор приложения  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Добавление назначения роли](./media/configure-aad/add-role-assignment.png)

1. Нажмите кнопку **Сохранить**.

### <a name="grant-node-level-access"></a>Доступ к узлам Гранта

Вы можете предоставить доступ на уровне узла, перенаправившись в систему безопасности узлов и нажмите на имя узла, которое вы хотите предоставить.

Выберите роль доступа к узловум блокчейну (Preview) и добавьте объект Azure AD ID, к который вы хотите предоставить доступ.

Для получения дополнительной информации [см.](configure-transaction-nodes.md#azure-active-directory-access-control)

## <a name="connect-using-azure-blockchain-connector"></a>Подключение с помощью блокчейн-разъема Azure

Скачать или клонировать [блокчейн-разъем Azure от GitHub.](https://github.com/Microsoft/azure-blockchain-connector/)

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Следуйте разделу quickstart в **readme,** чтобы построить разъем из исходного кода.

### <a name="connect-using-an-azure-ad-user-account"></a>Подключение с помощью учетной записи пользователя Azure AD

1. Запустите следующую команду для проверки подлинности с помощью учетной записи пользователя Azure AD. Замените \<myAADDirectory\> доменом Azure AD. Например, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Запросы Azure AD для учетных данных.
1. Вопийте с вашим именем пользователя и паролем.
1. После успешной аутентификации ваш локальный прокси подключается к блокчейн-узлам. Теперь вы можете прикрепить ваш клиент Geth с локальной конечной точкой.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Подключение с помощью идентификатора приложения

Многие приложения аутентифицировать с помощью Azure AD используют идентификатор приложения вместо учетной записи пользователя Azure AD.

Чтобы подключиться к вашему узу с помощью идентификатора приложения, замените **aadauthcode** **aadclient.**

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Параметр | Описание |
|-----------|-------------|
| арендатор-id | Домен Azure AD, например,`yourdomain.onmicrosoft.com`
| client-id | Идентификатор клиента зарегистрированного приложения в Azure AD
| client-secret | Секрет клиента зарегистрированного приложения в Azure AD

Для получения дополнительной информации о том, как зарегистрировать приложение в Azure AD, [см.](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Подключение мобильного устройства или текстового браузера

Для мобильного устройства или текстового браузера, где всплывающий дисплей аутентификации Azure AD невозможен, Azure AD генерирует одноразовый пароль. Вы можете скопировать пароль и продолжить проверку подлинности Azure AD в другой среде.

Для создания пароля замените **aadauthcode** **на aaddevice.** Замените \<myAADDirectory\> доменом Azure AD. Например, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о безопасности [Azure Blockchain Service security](data-security.md)данных в Azure Blockchain Service см.