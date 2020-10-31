---
title: Проверка подлинности в Azure Key Vault
description: Узнайте, как выполнять проверку подлинности в Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 3ec831bada19aa8d3872440ba628ac06bc64f749
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099425"
---
# <a name="authenticate-to-azure-key-vault"></a>Проверка подлинности в Azure Key Vault

Azure Key Vault позволяет хранить секреты и управлять их распределением в централизованном и защищенном облачном репозитории. При этом нет необходимости хранить учетные данные в приложениях. Для доступа к этим секретам во время выполнения приложению достаточно пройти проверку подлинности в Key Vault.

## <a name="app-identity-and-security-principals"></a>Удостоверение приложения и субъекты безопасности

Проверка подлинности в Key Vault работает в сочетании со службой [Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis), которая отвечает за проверку подлинности удостоверений любого предоставленного **субъекта безопасности** .

Субъект безопасности — это объект, представляющий пользователя, группу, службу или приложение, которые запрашивают доступ к ресурсам Azure. Azure назначает каждому субъекту безопасности уникальный **идентификатор объекта** .

* Субъект безопасности **Пользователь** определяет конкретного человека, у которого есть профиль в Azure Active Directory.

* Субъект безопасности **Группа** определяет набор пользователей, созданных в Azure Active Directory. Все роли или разрешения, назначенные группе, предоставляются всем пользователям в этой группе.

* Особый тип субъекта безопасности **Субъект-служба** служит идентификатором для приложения или службы, то есть сопоставляется с определенным фрагментом кода, а не с пользователем или группой. Идентификатор объекта для субъекта-службы называется **идентификатором клиента** и применяется аналогично имени пользователя. **Секрет клиента** выполняет для субъекта-службы функции пароля.

У приложений есть два способа получить субъект-службу:

* Создайте для приложения **управляемое удостоверение** , назначаемое системой (рекомендуется).

    При использовании управляемого удостоверения Azure самостоятельно управляет субъектом-службой приложения и автоматически выполняет для него проверку подлинности в других службах Azure. Управляемое удостоверение доступно для приложений, развернутых в разных службах.

    Дополнительные сведения см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](/azure/active-directory/managed-identities-azure-resources/overview) Также просмотрите статью [Службы с поддержкой управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities), где собраны ссылки на статьи с инструкциями по включению управляемых удостоверений для конкретных служб (Служба приложений, Функции Azure, Виртуальные машины и т. д.).

* Если вы не можете использовать управляемое удостоверение, **зарегистрируйте** приложение в клиенте Azure AD, как описано в статье [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app). Кроме того, при регистрации создается второй объект приложения, который представляет это приложение для всех клиентов.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Авторизация доступа к Key Vault для субъекта безопасности

Key Vault поддерживает два отдельных уровня авторизации.

- **Политики доступа** определяют, разрешено ли пользователю, группе или субъекту-службе обращаться к секретам, ключам и сертификатам, размещенным *внутри* существующего ресурса Key Vault (так называемые "операции плоскости данных"). Политики доступа обычно сопоставляются с пользователями, группами и приложениями.

    Чтобы назначить политики доступа, воспользуйтесь следующими руководствами:

    - [Портал Azure](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Разрешения роли** определяют, разрешено ли пользователю, группе или субъекту-службе создавать или удалять ресурсы Key Vault и иным образом управлять ими (так называемые "операции плоскости управления"). Такие роли обычно предоставляются только администраторам.
 
    Сведения о назначении ролей и управлении ими вы найдете в следующих статьях:

    - [Портал Azure](/azure/role-based-access-control/role-assignments-portal)
    - [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
    - [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)

    Сейчас Key Vault поддерживает роль [Участник](/azure/role-based-access-control/built-in-roles#key-vault-contributor), которая позволяет выполнять операции управления с ресурсами Key Vault. Также предоставляются предварительные версии некоторых других ролей. Кроме того, вы можете создавать пользовательские роли, как описано в статье [Пользовательские роли Azure](/azure/role-based-access-control/custom-roles).

    Общие сведения о ролях см. в статье [Что такое управление доступом на основе ролей в Azure (RBAC)?](/azure/role-based-access-control/overview).


> [!IMPORTANT]
> Для повышения уровня безопасности всегда соблюдайте принцип минимальных привилегий, предоставляя только те конкретные политики и роли доступа, которые необходимы для работы. 
    
## <a name="configure-the-key-vault-firewall"></a>Настройка брандмауэра Key Vault

По умолчанию Key Vault разрешает доступ к ресурсам с общедоступных IP-адресов. Чтобы повысить уровень безопасности, вы можете ограничить доступ, разрешая его только для конкретных диапазонов IP-адресов, конечных точек служб, виртуальных сетей или частных конечных точек.

Дополнительные сведения см. в статье [Доступ к Azure Key Vault из-за брандмауэра](/azure/key-vault/general/access-behind-firewall).


## <a name="the-key-vault-authentication-flow"></a>Поток проверки подлинности Key Vault

1. Субъект-служба запрашивает проверку подлинности в Azure AD, например:
    * пользователь входит на портал Azure, вводя имя пользователя и пароль;
    * приложение вызывает REST API Azure, предоставляя идентификатор и секрет или сертификат клиента;
    * ресурс Azure, например виртуальная машина с управляемым удостоверением, обращается к конечной точке REST [Службы метаданных экземпляров (IMDS) Azure](/azure/virtual-machines/windows/instance-metadata-service) для получения маркера доступа.

1. Если проверка подлинности в Azure AD проходит успешно, субъекту-службе предоставляется маркер OAuth.

1. Субъект-служба вызывает REST API Key Vault через конечную точку (URI) Key Vault.

1. Брандмауэр Key Vault проверяет указанные ниже условия. Если любое из них соблюдается, вызов разрешается. Иначе вызов блокируется и возвращается соответствующий ответ:

    * брандмауэр отключен, то есть общедоступная конечная точка Key Vault считается доступной из общедоступного Интернета;
    * вызывающий объект является [доверенной службой Key Vault](/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services), то есть к нему не применяются правила брандмауэра;
    * вызывающий объект указан в брандмауэре в списке разрешенных IP-адресов, виртуальных сетей или конечных точек служб;
    * вызывающий объект может обращаться к Key Vault через настроенное подключение к приватному каналу.    

1. Если брандмауэр разрешает вызов, Key Vault обращается к Azure AD для проверки маркера доступа субъекта-службы.

1. Key Vault проверяет, есть ли для субъекта-службы необходимая политика доступа для запрошенной операции. Если политики нет, Key Vault возвращает ответ с запретом доступа.

1. Key Vault выполняет запрошенную операцию и возвращает результат.

На следующей схеме показан пример обращения приложения к интерфейсу API "Получение секрета" в Key Vault:

![Поток проверки подлинности в Key Vault](../media/authentication/authentication-flow.png)

## <a name="code-examples"></a>Примеры кода

В следующей таблице собраны ссылки на статьи, которые демонстрируют работу с Key Vault в коде приложения на разных языках с использованием соответствующей библиотеки пакета средств разработки для Azure. Там же для удобства представлены и другие интерфейсы, такие как Azure CLI и портал Azure.

| Секреты Key Vault | Ключи Key Vault | Сертификаты Key Vault |
|  --- | --- | --- |
| [Python](/azure/key-vault/secrets/quick-create-python) | [Python](/azure/key-vault/keys/quick-create-python) | [Python](/azure/key-vault/certificates/quick-create-python) | 
| [Пакет SDK для .NET версии 4](/azure/key-vault/secrets/quick-create-net) | -- | -- |
| [Пакет SDK для .NET версии 3](https://dotnet.microsoft.com/download/dotnet-core/3.0) | -- | -- |
| [Java](/azure/key-vault/secrets/quick-create-java) | -- | -- |
| [JavaScript](/azure/key-vault/secrets/quick-create-node) | -- | -- | 
| | | |
| [Портал Azure](/azure/key-vault/secrets/quick-create-portal) | [Портал Azure](/azure/key-vault/keys/quick-create-portal) | [Портал Azure](/azure/key-vault/certificates/quick-create-portal) |
| [Azure CLI](/azure/key-vault/secrets/quick-create-cli) | [Azure CLI](/azure/key-vault/keys/quick-create-cli) | [Azure CLI](/azure/key-vault/certificates/quick-create-cli) |
| [Azure PowerShell](/azure/key-vault/secrets/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/keys/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/certificates/quick-create-powershell) |
| [Шаблон ARM](/azure/key-vault/secrets/quick-create-net) | -- | -- |

## <a name="next-steps"></a>Next Steps

- [Устранение неполадок с политиками доступа Key Vault](troubleshooting-access-issues.md)
- [Коды ошибок REST API в Key Vault](rest-error-codes.md)
- [Руководство разработчика для Key Vault](developers-guide.md)
- [Что такое управление доступом на основе ролей в Azure (RBAC)?](/azure/role-based-access-control/overview)
