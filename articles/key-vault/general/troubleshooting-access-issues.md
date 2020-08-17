---
title: Устранение проблем с политикой доступа к хранилищу ключей Azure
description: Устранение проблем с политикой доступа к хранилищу ключей Azure
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.openlocfilehash: f775a0e1dcb82afc9a458ce4ee608a52ec06dc4c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135918"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Устранение проблем с политикой доступа к хранилищу ключей Azure

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Как можно определить, как и когда осуществляется доступ к хранилищам ключей?
Создав одно или несколько хранилищ ключей вы, вероятно, захотите отслеживать, кто, как и когда осуществлял доступ к этим хранилищам. Это можно сделать, включив ведение журнала для Azure Key Vault. Пошаговые инструкции по включению ведения журнала см. в [этой статье](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Как отслеживать доступность хранилища, периоды задержки веб-службы или другие метрики производительности для хранилища ключей?
После начала масштабирования службы количество запросов, отправленных в хранилище ключей, будет возрастать. Это может увеличить задержку запросов и в крайних случаях привести к их регулированию, что повлияет на производительность службы. Вы можете отслеживать метрики производительности хранилища ключей и получать оповещения по конкретным пороговым значениям. Пошаговые инструкции по настройке мониторинга см. в [этой статье](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Как можно назначить управление доступом для каждого объекта хранилища ключей? 
Сведения о доступности функции управления доступом для каждого секрета, ключа, сертификата см [здесь](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/.32213176-per-secret-key-certificate-access-control).

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Как обеспечить проверку подлинности хранилища ключей с помощью политики управления доступом?
Самый простой способ проверить подлинность облачного приложения в Key Vault — с помощью управляемых удостоверений. Дополнительные сведения см. в статье об [использовании управляемого удостоверения Службы приложений для доступа к Azure Key Vault]( https://docs.microsoft.com/azure/key-vault/general/managed-identity).
Если вы создаете локальное приложение, выполняете локальную разработку или не можете использовать управляемое удостоверение, вы можете зарегистрировать субъект-службу вручную и предоставить доступ к хранилищу ключей с помощью политики управления доступом. Дополнительные сведения см. в [этой статье](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps).


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Как предоставить группе AD доступ к хранилищу ключей?
Предоставьте разрешения группе AD для хранилища ключей с помощью команды Azure CLI az keyvault set-policy или командлета Azure PowerShell Set-AzKeyVaultAccessPolicy. Примеры см. в разделе о [предоставлении приложению, группе Azure AD или пользователю доступа к хранилищу ключей](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault).

Приложению также требуется по крайней мере одна роль Системы управления идентификацией и доступом (IAM), назначенная хранилищу ключей. В противном случае оно не сможет войти в систему и завершится ошибкой о недостаточных правах доступа к подписке. На обновление токена и активацию группам Azure AD с управляемыми удостоверениями может потребоваться до 8 часов.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Как выполнить повторное развертывание Key Vault с помощью шаблона Resource Manager без удаления существующих политик доступа?
В настоящее время при повторном развертывании шаблонов Resource Manager Key Vault будет удалена любая политика доступа в Key Vault. Она будет заменена политикой доступа в шаблоне Resource Manager. Для политик доступа Key Vault не существует добавочного параметра. Чтобы сохранить политики доступа в Key Vault, необходимо считать существующие политики доступа в Key Vault и заполнить ими шаблон Resource Manager для избежания сбоев при доступе.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Рекомендуемые действия по устранению неполадок для указанных ниже типов ошибок:
* HTTP 401: запрос не прошел проверку подлинности — [действия по устранению неполадок](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request);
* HTTP 403: недостаточно разрешений — [действия по устранению неполадок](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions);
* HTTP 429: слишком много запросов — [действия по устранению неполадок](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests).
* Проверьте, есть ли у вас разрешение на удаление в Key Vault [Политики доступа Key Vault](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* Если у вас возникли проблемы при проверке подлинности в Key Vault в коде, используйте [пакет SDK проверки подлинности](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html).

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Каковы рекомендации при регулировании хранилища ключей?
Следуйте рекомендациям, описанным [здесь](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits).

## <a name="next-steps"></a>Next Steps

Узнайте, как устранять неполадки при проверке подлинности в хранилище ключей. [Руководство по устранению неполадок в Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
