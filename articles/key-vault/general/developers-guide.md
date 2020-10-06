---
title: Руководство разработчика хранилища ключей Azure
description: Разработчики могут использовать хранилище ключей Azure для управления криптографическими ключами в среде Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 662c23a29e383800a4591c900e02133c16fa2090
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743324"
---
# <a name="azure-key-vault-developers-guide"></a>Руководство разработчика хранилища ключей Azure

Хранилище Key Vault обеспечивает безопасный доступ к конфиденциальной информации из приложений:

- Ключи, секреты и сертификаты защищаются без необходимости написания кода, и вы можете легко использовать их из своих приложений.
- Вы можете предоставить клиентам возможность владеть собственными ключами, секретными данными и сертификатами и управлять ими, чтобы вы могли сосредоточиться на предоставлении основных компонентов программного обеспечения. Таким образом ваши приложения не будут нести ответственность или потенциальную ответственность за ключи клиента, секреты и сертификаты клиентов.
- Приложение может использовать ключи для подписывания и шифрования, но позволяет управлять ключами вне приложения. Дополнительные сведения о ключах см. в разделе [о ключах](../keys/about-keys.md) .
- Вы можете управлять учетными данными, такими как пароли, ключи доступа, маркеры SAS, сохраняющие их в Key Vault как секреты, см. [сведения о секретах](../secrets/about-secrets.md) .
- Управление сертификатами. Дополнительные сведения см. в разделе [About Certificates](../certificates/about-certificates.md) .

Дополнительные сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](overview.md).

## <a name="public-previews"></a>Общедоступные предварительные версии

Периодически мы выпускаем общедоступные предварительные версии нового компонента Key Vault. Испытайте общедоступные функции предварительной версии и сообщите нам о том, что вы думаете azurekeyvault@microsoft.com с помощью нашего адреса электронной почты для отзывов.

## <a name="creating-and-managing-key-vaults"></a>Создание хранилищ ключей и управление ими

Управление Key Vault, аналогичное другим службам Azure, выполняется с помощью службы Azure Resource Manager. Azure Resource Manager — это служба развертывания и управления для Azure. Она обеспечивает уровень управления для создания, обновления и удаления ресурсов в учетной записи Azure. Дополнительные сведения см. в разделе [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

Доступом к слою управления управляет служба [управления доступом на основе ролей Azure](https://docs.microsoft.com/azure/role-based-access-control/overview). В Key Vault, уровень управления, также называемый плоскостью управления или управления, позволяет создавать и администрировать хранилища ключей и его атрибуты, включая политики доступа, но не ключи, секреты и сертификаты, управляемые на плоскости данных. Вы можете использовать предварительно определенную `Key Vault Contributor` роль для предоставления доступа к управлению Key Vault.     

**API и пакеты SDK для управления хранилищем ключей:**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Ссылки](/cli/azure/keyvault)<br>[Краткое руководство](quick-create-cli.md)|[Ссылки](/powershell/module/az.keyvault)<br>[Краткое руководство](quick-create-powershell.md)|[Ссылки](/rest/api/keyvault/)|[Ссылки](/azure/templates/microsoft.keyvault/vaults)|[Ссылки](/dotnet/api/microsoft.azure.management.keyvault)|[Ссылки](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Ссылки](/java/api/com.microsoft.azure.management.keyvault)|[Ссылки](/javascript/api/@azure/arm-keyvault)|

См. раздел [клиентские библиотеки](client-libraries.md) для пакетов установки и исходный код.

Дополнительные сведения о Key Vault плоскости управления см. в разделе [Key Vault Management плоскость](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac) .

## <a name="authenticate-to-key-vault-in-code"></a>Проверка подлинности в коде Key Vault

Key Vault использует проверку подлинности Azure AD, которая требует наличия субъекта безопасности Azure AD для предоставления доступа. Субъект безопасности Azure AD может быть пользователем, субъектом-службой приложения, [управляемым удостоверением для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md)или группой участников безопасности любого типа.

### <a name="authentication-best-practices"></a>Рекомендации по проверке подлинности
Рекомендуется использовать управляемое удостоверение для приложений, развернутых в Azure. Если вы используете службы Azure, которые не поддерживают управляемое удостоверение или если приложения развертываются локально, [субъект-служба с сертификатом](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) является возможной альтернативой. В этом случае сертификат должен храниться в Key Vault и поворачиваться часто. Субъект-службу с секретом можно использовать для сред разработки и тестирования, а также локально или в Cloud Shell с помощью субъекта пользователя.

Описанные выше сценарии проверки подлинности поддерживаются клиентской библиотекой удостоверений Azure и интегрированы с пакетами SDK для Key Vault. Библиотеку удостоверений Azure можно использовать в разных средах и платформах без изменения кода. Удостоверение Azure также автоматически получает маркер проверки подлинности из входа в Azure с помощью Azure CLI, Visual Studio, Visual Studio Code и других. 

Дополнительные сведения можно найти в разделе 

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Пакет SDK для Azure Identity .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)|[Пакет SDK для удостоверений Azure Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)|[Пакет SDK для Azure Identity Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)|     

## <a name="manage-keys-certificates-and-secrets"></a>Управление ключами, сертификатами и секретами

Доступом к ключам, секретам и сертификатам управляет плоскость данных. Управление доступом к плоскости данных можно выполнить с помощью политик доступа к локальному хранилищу или RBAC (Предварительная версия).

**Ключи API и пакеты SDK**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Ссылки](/cli/azure/keyvault/key)<br>[Краткое руководство](../keys/quick-create-cli.md)|[Ссылки](/powershell/module/az.keyvault/)<br>[Краткое руководство](../keys/quick-create-powershell.md)|[Ссылки](/rest/api/keyvault/#key-operations)|Недоступно|[Ссылки](/dotnet/api/azure.security.keyvault.keys)|[Ссылки](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Краткое руководство](../keys/quick-create-python.md)|[Ссылки](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)|[Ссылки](/javascript/api/@azure/keyvault-keys/)|

**Интерфейсы API и пакеты SDK для сертификатов**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Ссылки](/cli/azure/keyvault/certificate)<br>[Краткое руководство](../certificates/quick-create-cli.md)|[Ссылки](/powershell/module/az.keyvault)<br>[Краткое руководство](../certificates/quick-create-powershell.md)|[Ссылки](/rest/api/keyvault/#certificate-operations)|Недоступно|[Ссылки](/dotnet/api/azure.security.keyvault.certificates)|[Ссылки](/python/api/overview/azure/keyvault-certificates-readme)<br>[Краткое руководство](../certificates/quick-create-python.md)|[Ссылки](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)|[Ссылки](/javascript/api/@azure/keyvault-certificates/)|

**Секреты API и пакеты SDK**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Ссылки](/cli/azure/keyvault/secret)<br>[Краткое руководство](../secrets/quick-create-cli.md)|[Ссылки](/powershell/module/az.keyvault/)<br>[Краткое руководство](../secrets/quick-create-powershell.md)|[Ссылки](/rest/api/keyvault/#secret-operations)|[Ссылки](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Краткое руководство](../secrets/quick-create-template.md)|[Ссылки](/dotnet/api/azure.security.keyvault.secrets)<br>[Краткое руководство](../secrets/quick-create-net.md)|[Ссылки](/python/api/overview/azure/keyvault-secrets-readme)<br>[Краткое руководство](../secrets/quick-create-python.md)|[Ссылки](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Краткое руководство](../secrets/quick-create-java.md)|[Ссылки](/javascript/api/@azure/keyvault-secrets/)<br>[Краткое руководство](../secrets/quick-create-node.md)|

См. раздел [клиентские библиотеки](client-libraries.md) для пакетов установки и исходный код.

Дополнительные сведения о Key Vault безопасности плоскости данных см. в статьях [Key Vault плоскость данных и политики доступа](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) и [Key Vault ПЛОСКОСТЬ данных и RBAC (Предварительная версия)](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-azure-rbac-preview) .

### <a name="code-examples"></a>Примеры кода

Полные примеры использования хранилища ключей с приложениями см. в следующих документах:

- [Примеры кода Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) — примеры кода для Azure Key Vault. 

## <a name="how-tos"></a>Практические руководства

В следующих статьях приводятся рекомендации по решению конкретных задач при работе с Azure Key Vault:

- [Доступ к хранилищу ключей под защитой брандмауэра](access-behind-firewall.md). Для доступа к хранилищу ключей необходимо, чтобы клиентское приложение имело доступ к нескольким конечным точкам, требуемым для различных функций.
- Сведения о развертывании сертификатов на виртуальных машинах из Key Vault — [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows), [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) — облачное приложение, работающее на виртуальной машине в Azure, нуждается в сертификате. Как получить этот сертификат для виртуальной машины?
- [Развертывание сертификата веб-приложения Azure через Key Vault](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
- Назначение политики доступа (портал[CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  [Portal](assign-access-policy-portal.md)). 
- В разделе [Как использовать обратимое удаление в Key Vault с помощью интерфейса командной строки](soft-delete-cli.md) описывается использование и жизненный цикл хранилища ключей, а также различных объектов хранилища ключей с возможностью обратимого удаления.
- [Передача безопасных значений (например, паролей) во время развертывания](../../azure-resource-manager/templates/key-vault-parameter.md) — Если в процессе развертывания в качестве параметра необходимо передать безопасное значение (например, пароль), его можно сохранить как секретный код в хранилище ключей Azure и вставить ссылку на это значение в другие шаблоны Resource Manager.

## <a name="integrated-with-key-vault"></a>Интеграция с хранилищем ключей

Эти статьи посвящены другим сценариям и службам, использующим Key Vault или интегрирующимся с ним.

- [Шифрование неактивных с помощью Key Vault](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) позволяет управлять собственным ключом клиента. Например, вместо того, чтобы вашим ключом клиента управляла корпорация Майкрософт (по умолчанию), вы можете сами управлять им в соответствии с определенными нормами своей организации. Сценарий с использованием собственного ключа клиента называется BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Основные сведения о Key Vault

- [Общие сведения о функции обратимого удаления в Azure Key Vault](soft-delete-overview.md). Описание функции, которая позволяет восстанавливать случайно или намеренно удаленные объекты.
- [Руководство по регулированию Azure Key Vault](overview-throttling.md). Описание основных механизмов регулирования числа запросов и подходов к созданию приложения.
- [Системы безопасности и географические ограничения Azure Key Vault](overview-security-worlds.md). Описание взаимосвязей между регионами и областями безопасности.

## <a name="social"></a>Социальные сети

- [Блог хранилища ключей](https://aka.ms/kvblog)
- [Форум хранилища ключей](https://aka.ms/kvforum)
