---
title: Шифрование на отдыхе с помощью ключей, управляемых клиентом
titleSuffix: Azure Cognitive Search
description: Дополнение шифрования сервера через индексы и синонимы карты в Azure Cognitive Search с помощью ключей, которые вы создаете и управляете в Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899948"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Шифрование содержимого в Azure Cognitive Search с использованием ключей, управляемых клиентами в Хранилище ключей Azure

По умолчанию Azure Cognitive Search шифрует индексированный контент в состоянии покоя [с помощью сервис-управляемых ключей.](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models) Вы можете дополнить шифрование по умолчанию дополнительным слоем шифрования с помощью ключей, которые вы создаете и управляете в Azure Key Vault. Эта статья идет вас через шаги.

Шифрование на стороне сервера поддерживается через интеграцию с [Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-overview) Можно создавать собственные ключи шифрования и хранить их в хранилище ключей либо использовать API-интерфейсы Azure Key Vault для генерации этих ключей. С помощью Azure Key Vault можно также провести аудит использования ключей. 

Шифрование с ключами, управляемыми клиентом, настраивается на уровне индекса или синонима карты при создании этих объектов, а не на уровне поисковой службы. Вы не можете шифровать уже существующий контент. 

Ключи не все должны быть в том же ключе Vault. Один поисковый сервис может размещать несколько зашифрованных индексов или синонимов карт, каждый из которых зашифрован с помощью собственных ключей шифрования, управляемых клиентом, хранящихся в разных ключевых убежищах.  Вы также можете иметь индексы и синонимы карты в той же службе, которые не зашифрованы с помощью ключей, управляемых клиентом. 

> [!IMPORTANT] 
> Эта функция доступна на [REST API версии 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) и [.NET SDK версия 8.0-предварительный просмотр](search-dotnet-sdk-migration-version-9.md). В настоящее время на портале Azure отсутствует поддержка для настройки ключей шифрования, управляемых клиентом. Поисковая служба должна быть создана после января 2019 года и не может быть бесплатной (общей) услугой.

## <a name="prerequisites"></a>Предварительные требования

В этом примере используются следующие службы. 

+ [Создайте службу "Когнитивный поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. 

+ [Создайте ресурс Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) или найдите существующее хранилище под подпиской.

+ Для задач конфигурации используется [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) или [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli)

+ [Почтальон,](search-get-started-postman.md) [Azure PowerShell](search-create-index-rest-api.md) и [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) могут быть использованы для вызова REST API. В настоящее время нет портала поддержки шифрования, управляемого клиентом.

>[!Note]
> Из-за характера шифрования с функцией ключей, управляемых клиентом, Azure Cognitive Search не сможет получить ваши данные, если ключ свода azure Key будет удален. Чтобы предотвратить потерю данных, вызванную случайными удалениями ключей Key Vault, **необходимо** включить защиту от мягкого удаления и очистки в Key Vault, прежде чем его можно будет использовать. Для получения дополнительной [Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)информации см.   

## <a name="1---enable-key-recovery"></a>1 - Включить восстановление ключей

После создания ресурса Azure Key Vault включите **защиту мягкого удаления** и **очистки** в выбранном хранилище ключей, выполняя следующие команды PowerShell или Azure CLI:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Создайте новый ключ

Если вы используете существующий ключ для шифрования содержимого Azure Cognitive Search, пропустите этот шаг.

1. [Войдите на портал Azure](https://portal.azure.com) и перейдите к панели мониторинга хранилища ключей.

1. Выберите настройки **ключей** из левой панели навигации и нажмите **кнопку «Генерировать/импорт».**

1. В области **Создание ключа** откройте список **Параметры** и выберите метод создания ключа. Вы можете **создать** новый ключ, **отправить** существующий или **восстановить резервную копию** ключа.

1. Введите **имя** для ключа и по желанию выберите другие ключевые свойства.

1. Нажмите на кнопку **«Создание»,** чтобы начать развертывание.

Сделайте примечание Key Identifier - это состоит из **ключевого значения Uri**, **ключевое имя**, и **ключевая версия**. Для определения зашифрованного индекса в Azure Cognitive Search вам понадобится их.
 
![Создание нового ключа хранилища ключей](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Создание нового ключа хранилища ключей")

## <a name="3---create-a-service-identity"></a>3 - Создание итога службы

Присвоение удостоверения личности службе поиска позволяет предоставлять доступ к Key Vault службе поиска. Служба поиска будет использовать свою идентификацию для проверки подлинности с помощью хранилища ключей Azure.

Azure Cognitive Search поддерживает два способа присвоения итога: управляемое удостоверение личности или управляемое извне приложение Active Directory Azure. 

Если возможно, используйте управляемую идентификацию. Это самый простой способ присвоения идентификации службе поиска и должен работать в большинстве сценариев. Если вы используете несколько ключей для индексов и синонимных карт, или если ваше решение находится в распределенной архитектуре, которая дисквалифицирует аутентификацию на основе идентификации, используйте расширенный подход, управляемый внешним управлением [Azure Active Directory,](#aad-app) описанный в конце этой статьи.

 Как правило, управляемая идентификация позволяет службе поиска аутентифицировать проверку в Azure Key Vault без хранения учетных данных в коде. Жизненный цикл этого типа управляемой идентификации привязан к жизненному циклу поисковой службы, которая может иметь только одну управляемую идентификацию. [Узнайте больше об управляемых идентификационных данных.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. Чтобы создать управляемую идентификацию, [вопийте на портале Azure](https://portal.azure.com) и откройте панель мониторинга поискового сервиса. 

1. Нажмите **Identity** в левом панели навигации, изменить свой статус **на On,** и нажмите **Сохранить**.

![Включить управляемую идентификацию](./media/search-enable-msi/enable-identity-portal.png "Включить manged личности")

## <a name="4---grant-key-access-permissions"></a>4 - Разрешение доступа к ключу Гранта

Чтобы служба поиска могла использовать ключ Key Vault, вам необходимо предоставить службе поиска определенные разрешения на доступ.

Разрешения на доступ могут быть аннулированы в любой момент времени. После отзыва любой индекс поисковой службы или карта синонимов, используюих это хранилище ключей, станет непригодным для использования. Восстановление разрешений на доступ к хранилищу ключей на более позднем этапе восстановит доступ к карте индекса. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

1. [Вопийте на портале Azure](https://portal.azure.com) и откройте страницу обзора хранилища ключей. 

1. Выберите **настройки политики доступа** из левой панели навигации и нажмите **кнопку «Добавить новый.**

   ![Добавление новой политики доступа к хранилищу ключей](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Добавление новой политики доступа к хранилищу ключей")

1. Нажмите **«Выберите основной выбор»** и выберите службу когнитивного поиска Azure. Вы можете искать его по имени или по идентификатору объекта, который был отображан после включения управляемого идентификатора.

   ![Выберите принцип политики доступа к хранилищу ключей](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Выберите принцип политики доступа к хранилищу ключей")

1. Нажмите на **ключевые разрешения** и выберите *Get,* *Unwrap Key* и *Wrap Key.* Для быстрого выбора необходимых разрешений можно использовать шаблон *хранения озер данных Azure или Azure.*

   Azure Cognitive Search должен быть предоставлен со [следующими разрешениями на доступ:](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)

   * *Получить* - позволяет вашей поисковой службы для получения общедоступных частей вашего ключа в Key Vault
   * *Wrap Key* - позволяет поисковой службе использовать ваш ключ для защиты внутреннего ключа шифрования
   * *Unwrap Key* - позволяет поисковой службе использовать ваш ключ для разворачивания внутреннего ключа шифрования

   ![Выберите ключевые разрешения политики доступа к хранилищу](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Выберите ключевые разрешения политики доступа к хранилищу")

1. Нажмите **OK** и **сохраните** изменения политики доступа.

> [!Important]
> Зашифрованное содержимое в Azure Cognitive Search настроено на использование определенного ключа Azure Key Vault с определенной **версией.** При изменении ключа или версии, индекс или синоним карты должны быть обновлены, чтобы использовать новую версию **ключа, прежде чем** удалять предыдущую версию ключа. В противном случае индекс или синоним карты непригодны для угодья, при этом вы не сможете расшифровать содержимое после потери доступа к ключу.   

## <a name="5---encrypt-content"></a>5 - Шифрование контента

Создание индексной или синонимной карты, зашифрованной с помощью ключей, управляемых клиентом, пока не представляется возможным с помощью портала Azure. Для создания такого индекса или синонимной карты используйте Azure Cognitive Search REST API.

Как индекс, так и синонимная карта поддерживают новое свойство шифрования верхнего **уровняKey,** используемое для определения ключа. 

Используя **хранилище ключей Uri,** **ключевое имя** и **ключевую версию** ключа хранилища ключа, мы можем создать определение **encryptionKey:**

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Ни одна из этих сведений о хранилище ключей не считается секретной и может быть легко извлечена при просмотре соответствующей ключевой страницы Azure Key Vault на портале Azure.

Если вы используете приложение AAD для проверки подлинности Key Vault вместо управляемой идентификации, добавьте **учетные данные доступа** к приложению AAD в ключ шифрования: 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>Пример: шифрование индекса
Детали создания нового индекса через REST API можно найти на конференции [Create Index (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-index)где единственным отличием здесь является указание ключевых деталей шифрования как часть определения индекса: 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Теперь можно отправить запрос на создание индекса, а затем начать использовать индекс в обычном режиме.

## <a name="example-synonym-map-encryption"></a>Пример: шифрование карты Синонимов

Детали создания новой карты синонимов через REST API можно найти на карте [Create Synonym Map (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)где единственным отличием здесь является указание ключевых деталей шифрования как часть определения карты синонимов: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Теперь вы можете отправить запрос на создание синонимов карты, а затем начать использовать его в обычном режиме.

>[!Important] 
> Хотя **encryptionKey** не может быть добавлен к существующим индексам когнитивного поиска Azure или синонимным картам, он может быть обновлен, предоставляя различные значения для любой из трех ключевых сведений хранилища (например, обновление ключевой версии). При переходе на новый ключ-убежище или новую ключевую версию любой индекс когнитивного поиска Azure или синонимкарты, использующие ключ, должны быть сначала обновлены, чтобы использовать новую версию **ключа, прежде чем** удалять предыдущую версию ключа. В противном случае индекс или синоним карты непригодны для угодья, так как она не сможет расшифровать содержимое после потери доступа к ключу.   
> Восстановление разрешений доступа к хранилищу ключей на более позднем этапе восстановит доступ к содержимому.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Расширенный: Используйте приложение Active Directory, управляемое внешним управлением Azure

Если управляемая идентификация невозможна, можно создать приложение Active Directory Azure с принципом безопасности для службы когнитивного поиска Azure. В частности, управляемая личность нежизнеспособна в следующих условиях:

* Вы не можете напрямую предоставлять разрешения службы поиска на доступ в хранилище ключей (например, если служба поиска находится в другом арендаторе Active Directory, чем Вхранилище ключей Azure).

* Для размещения нескольких зашифрованных индексов требуется одна служба поиска, каждая из которых использует другой ключ из другого хранилища ключей, где каждое хранилище ключей должно использовать **различные иные итогия** для проверки подлинности. Если использование другой идентификации для управления различными хранилищами ключей не является обязательным требованием, рассмотрите возможность управляемого идентификации выше.  

Чтобы разместить такие топологии, Azure Cognitive Search поддерживает использование приложений Azure Active Directory (AAD) для проверки подлинности между службой поиска и Key Vault.    
Для создания приложения AAD на портале:

1. [Создайте приложение Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Получите идентификатор приложения и ключ проверки подлинности,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) так как они потребуются для создания зашифрованного индекса. Значения, которые необходимо предоставить, включают **идентификатор приложения** и **ключ проверки подлинности.**

>[!Important]
> При принятии решения об использовании приложения AAD аутентификации вместо управляемой идентификации, учитывайте тот факт, что Azure Cognitive Search не уполномочен управлять вашим приложением AAD от вашего имени, и вы должны управлять вашим приложением AAD, например периодически вращение ключа проверки подлинности приложения.
> При изменении приложения AAD или ключа проверки подлинности любой индекс когнитивного поиска Azure или синонимкарты, использующей это приложение, должны сначала быть обновлены, чтобы использовать новый ID-ключ **приложения, прежде чем** удалять предыдущее приложение или ключ авторизации, а затем отменять доступ к нему key Vault.
> В противном случае индекс или синоним карты непригодны для угодья, так как она не сможет расшифровать содержимое после потери доступа к ключу.   

## <a name="next-steps"></a>Дальнейшие действия

Если вы не знакомы с архитектурой безопасности Azure, просмотрите [документацию безопасности Azure](https://docs.microsoft.com/azure/security/)и, в частности, эту статью:

> [!div class="nextstepaction"]
> [Шифрование неактивных данных](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
