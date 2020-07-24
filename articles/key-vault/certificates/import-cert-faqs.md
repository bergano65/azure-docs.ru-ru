---
title: Часто задаваемые вопросы о Azure Key Vault импорте сертификатов
description: Часто задаваемые вопросы о Azure Key Vault импорте сертификатов
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 1063f7189de4bdf1aaca4a6d72c979476433c32f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098272"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Часто задаваемые вопросы о Azure Key Vault импорте сертификатов

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Как импортировать сертификат в Azure Key Vault?

Импорт сертификата — для операции импорта хранилище ключей Azure принимает два формата сертификата PEM и PFX. Несмотря на наличие PEM-файлов только в общедоступной части, требуется хранилище ключей Azure и принимает только PEM или PFX в папке файлов и с закрытым ключом. Следуйте указаниям [в руководстве по импорту сертификата](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>После импорта защищенного паролем сертификата в хранилище ключей с последующей его загрузкой не удается увидеть пароль, связанный с сертификатом?
    
Отправленный защищенный сертификат после хранения в хранилище ключей не будет сохранять связанный с ним пароль. Пароль нужен только для операции импорта. Хотя это и есть концепция по проектированию, вы всегда можете получить сертификат в качестве секрета и преобразовать из Base64 в PFX, добавив предыдущий пароль с помощью [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Как устранить ошибку "неверный параметр"? Какие форматы сертификатов поддерживаются для импорта в Key Vault?

При импорте сертификата необходимо убедиться, что ключ включен в сам файл. Если закрытый ключ имеется по отдельности в другом формате, необходимо объединить ключ с сертификатом. Некоторые центры сертификации предоставляют сертификаты в разных форматах, поэтому перед импортом сертификата убедитесь, что они находятся в формате PEM или PFX и что используется ключ RSA или ECC. Сведения о [требованиях к сертификатам](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) и [ключах сертификатов](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection)см. в этой статье.

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>Ошибка при импорте сертификата с помощью портала: "Что-то пошло не так". Как можно исследовать дальнейшие исследования?
    
Чтобы просмотреть более описательную ошибку, импортируйте файл сертификата с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) или [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Как разрешить "тип ошибки: отказано в доступе или пользователь не авторизован для импорта сертификата"?
    
Для этой операции требуются разрешения на сертификаты и импорт. Перейдите в расположение Key Vault. Вам необходимо предоставить пользователю соответствующие разрешения в разделе политик доступа. Перейдите к политикам Key Vault> доступа > добавить политику доступа > выберите разрешения на сертификат (или в том виде, в котором разрешения) > основной > поиска, а затем добавьте адрес электронной почты пользователя. [Узнайте больше о политиках доступа, связанных с сертификатами.](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Как разрешить "тип ошибки: конфликт при создании сертификата"?
    
Имя сертификата должно быть уникальным. Сертификат с таким же именем может находиться в состоянии обратимого удаления, а также в соответствии с [композицией сертификата](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) в хранилище ключей Azure. если в Key Vault с тем же именем, которое вы пытаетесь указать для сертификата, есть другой ключ или секрет, он завершится ошибкой, и вам потребуется удалить этот ключ или секрет или использовать другое имя для сертификата. [Просмотреть удаленный сертификат](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Почему я получаю сообщение "тип ошибки: длина знака слишком велика"?
    
* Длина имени субъекта сертификата ограничена символом 200 char
* Длина пароля сертификата ограничена символом 200 char

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Можно ли импортировать сертификат с истекшим сроком действия в хранилище ключей Azure?
    
Нет, сертификаты PFX с истекшим сроком действия не импортируются в Azure Key Vault.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Как можно преобразовать сертификат в правильный формат?

Вы можете попросить центр сертификации предоставить сертификат в нужном формате, а также средства сторонних производителей, которые могут помочь в преобразовании в правильный формат, однако корпорация Майкрософт не сможет порекомендовать дополнительные сведения о том, как получить сертификат в нужном формате.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Можно ли импортировать сертификаты из ЦС, не являющегося партнером?
Да, можно импортировать сертификаты из любого ЦС, но хранилище ключей не сможет автоматически продлить эти сертификаты. Вы сможете настроить уведомления по электронной почте, чтобы получать уведомления об истечении срока действия сертификата.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Если импортировать сертификат из ЦС партнеров, функция автоматического продления по-прежнему будет работать?
Да, необходимо убедиться, что после отправки вы указали Автоповорот в политике выдачи сертификата. Кроме того, изменения будут отражены до следующего цикла или версии сертификата.


## <a name="next-steps"></a>Дальнейшие действия

- [Сертификаты Azure Key Vault](/azure/key-vault/certificates/about-certificates)
