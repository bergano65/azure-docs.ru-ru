---
title: Настройка доступа к Azure Active Directory
description: Как настроить службу Блокчейн в Azure с доступом Azure Active Directory
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028220"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Настройка доступа к Azure Active Directory

В этой статье вы узнаете, как предоставить доступ и подключение к службе Azure Blockchain узлов, с помощью Azure Active Directory (Azure AD) пользователя, группы или идентификаторы приложений.

Azure AD обеспечивает управление удостоверениями на основе облака и позволяет использовать единое удостоверение для всего предприятия и доступ приложений в Azure. Служба Azure Blockchain интегрировано с Azure AD и предлагает преимущества, такие как идентификатор федерации, единого входа и многофакторной проверки подлинности.

## <a name="prerequisites"></a>Технические условия

* [Создание элемента блокчейн с помощью портала Azure](create-member.md)

## <a name="grant-access"></a>Предоставление доступа

Вы можете предоставить доступ на уровне узла и уровне членов. Предоставление прав доступа на уровне элементов в свою очередь предоставляет доступ ко всем узлам в элемент.

### <a name="grant-member-level-access"></a>Предоставление уровня доступа члена

Чтобы предоставить разрешение на доступ на уровне членов.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите к **управление доступом (IAM) > Добавить > добавить назначение роли**.
1. Выберите **узел доступа к членам Блокчейн (Предварительная версия)** роли и добавьте объект идентификатор Azure AD, вы хотите предоставить доступ к. Azure AD идентификатор объекта может быть:

    | Объект Azure AD | Пример |
    |-----------------|---------|
    | Пользователь Azure AD   | `frank@contoso.onmicrosoft.com` |
    | Группы Azure AD  | `sales@contoso.onmicrosoft.com` |
    | Идентификатор приложения  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Добавление назначения роли](./media/configure-aad/add-role-assignment.png)

1. Щелкните **Сохранить**.

### <a name="grant-node-level-access"></a>Предоставление доступа на уровне узла

1. Можно предоставить доступ на уровне узла, перейдя по адресу безопасности узла и щелкните имя узла, который вы хотите предоставить доступ.
1. Выберите роль узла доступа к членам Блокчейн (Предварительная версия) и добавьте идентификатор Azure AD объект, который вы хотите предоставить доступ к. 

## <a name="connect-using-azure-blockchain-connector"></a>Подключение с помощью Azure Blockchain соединителя

Скачайте или клонируйте [Блокчейн соединителя Azure из GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Выполните, работе с этим руководством статьи **readme** создать соединитель из исходного кода.

### <a name="connect-using-an-azure-ad-user-account"></a>Подключение с использованием учетной записи пользователя Azure AD

1. Выполните следующую команду для проверки подлинности с помощью учетной записи пользователя Azure AD. Замените \<myAADDirectory\> с доменом Azure AD. Например, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD запрашивает учетные данные.
1. Войдите, используя имя пользователя и пароль.
1. После успешной проверки подлинности локальный прокси-сервер подключается к узлу блокчейна. Теперь можно присоединить Geth клиента с локальной конечной точке.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Подключение с использованием идентификатора приложения

Во многих приложениях пройти аутентификацию в Azure AD, используя идентификатор приложения вместо учетной записи пользователя Azure AD.

Чтобы подключиться к узлу, используя идентификатор приложения, замените **aadauthcode** с **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Параметр | ОПИСАНИЕ |
|-----------|-------------|
| Идентификатор клиента | Домен Azure AD, например, `yourdomain.onmicrosoft.com`
| Идентификатор клиента | Идентификатор клиента для зарегистрированного приложения в Azure AD
| секрет клиента | Секрет клиента для зарегистрированного приложения в Azure AD

Дополнительные сведения о том, как зарегистрировать приложение в Azure AD см. в разделе [как: Создание приложения Azure Active Directory и субъект-службы с доступом к ресурсам с помощью портала](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Подключение мобильных устройств или текст браузера

Для мобильных устройств или текстовый браузер, где всплывающем экране проверки подлинности Azure AD не поддерживается Azure AD создает одноразовый секретный код. Можно скопировать секретный код и продолжить проверку подлинности Azure AD в другой среде.

Чтобы создать секретный код, замените **aadauthcode** с **aaddevice**. Замените \<myAADDirectory\> с доменом Azure AD. Например, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о безопасности данных в службе Azure Blockchain см. в разделе:

> [!div class="nextstepaction"]
> [Azure Blockchain службы безопасности](data-security.md)