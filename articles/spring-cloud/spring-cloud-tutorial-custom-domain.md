---
title: Руководство. Сопоставление существующего личного домена с Azure Spring Cloud
description: Сведения о том, как сопоставить существующее настраиваемое DNS-имя с Azure Spring Cloud.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: ff38f923f7b33c4bc893246970c1e47d33e59269
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780410"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Сопоставление существующего личного домена с Azure Spring Cloud
Технология DNS (служба доменных имен) предназначена для хранения имен узлов в сети. В этом руководстве показано, как с помощью записи CNAME сопоставить домен (например, www.contoso.com). Также мы защитим домен с помощью сертификата и покажем, как применить протокол TLS (или SSL). 

Сертификаты используются для шифрования веб-трафика. Сертификаты TLS/SSL можно хранить в Azure Key Vault. 

## <a name="prerequisites"></a>Предварительные требования
* Приложение, развернутое в Azure Spring Cloud (можно воспользоваться статьей [Краткое руководство. Запуск существующего приложения Azure Spring Cloud с помощью портала Azure](spring-cloud-quickstart-launch-app-portal.md) или применить любое существующее приложение).
* Доменное имя с доступом к реестру DNS для поставщика домена, например GoDaddy.
* Закрытый сертификат (т. е. ваш самозаверяющий сертификат) стороннего поставщика. Этот сертификат должен соответствовать домену.
* Развернутый экземпляр [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

## <a name="import-certificate"></a>Импорт сертификата 
Для импорта сертификата требуется, чтобы на диске существовал файл в кодировке PEM или PFX, и у вас был доступ к закрытому ключу. 

Чтобы передать сертификат в хранилище ключей, сделайте следующее:
1. Перейдите к экземпляру хранилища ключей.
1. В области навигации слева щелкните **Сертификаты**.
1. В меню сверху щелкните **Создать или импортировать**.
1. В диалоговом окне **Создание сертификата** в разделе **Метод создания сертификата** выберите значение `Import`.
1. В разделе **Отправка файла сертификата** найдите и выберите расположение сертификата.
1. В поле **Пароль** введите закрытый ключ сертификата.
1. Нажмите кнопку **Создать**.

    ![Импорт сертификата 1](./media/custom-dns-tutorial/import-certificate-a.png)

Чтобы предоставить Azure Spring Cloud доступ к хранилищу ключей перед импортом сертификата, сделайте следующее:
1. Перейдите к экземпляру хранилища ключей.
1. В области навигации слева щелкните **Политика доступа**.
1. В меню сверху щелкните **Добавить политику доступа**.
1. Введите сведения и нажмите кнопку **Добавить**, а затем **Сохранить**.

| Разрешение секрета | Разрешение сертификата | Выбор субъекта |
|--|--|--|
| Получить, перечислить | Получить, перечислить | Управление доменом Azure Spring Cloud |

![Импорт сертификата 2](./media/custom-dns-tutorial/import-certificate-b.png)

Вы также можете использовать Azure CLI, чтобы предоставить Azure Spring Cloud права на доступ к хранилищу ключей.

Получите идентификатор объекта с помощью следующей команды.
```
az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId
```

Предоставьте Azure Spring Cloud права на доступ на чтение к хранилищу ключей, заменив идентификатор объекта в следующей команде.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list --secret-permissions get list
``` 

Чтобы импортировать сертификат в Azure Spring Cloud, сделайте следующее:
1. Перейдите к экземпляру службы. 
1. На странице приложения в области навигации слева выберите **Параметры TLS/SSL**.
1. Теперь щелкните **Импорт сертификата Key Vault**.

    ![Импорт сертификата](./media/custom-dns-tutorial/import-certificate.png)

Вы также можете использовать Azure CLI для импорта сертификата.

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> Перед выполнением указанной выше команды для импорта сертификата, убедитесь, что вы предоставили Azure Spring Cloud права на доступ к хранилищу ключей. Если права на доступ не предоставлены, вы можете выполнить следующую команду, чтобы сделать это.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

После успешного импорта сертификат появится в списке **Сертификаты закрытых ключей**.

![Сертификат закрытого ключа](./media/custom-dns-tutorial/key-certificates.png)

Вы также можете использовать Azure CLI для отображения списка сертификатов.

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
> Чтобы защитить личный домен с помощью этого сертификата, необходимо создать привязку сертификата к этому домену. Выполните инструкции из раздела **Добавление привязки SSL** в этой статье.

## <a name="add-custom-domain"></a>Добавление личного домена
Пользовательское DNS-имя можно сопоставить c Azure Spring Cloud с помощью записи CNAME. 

> [!NOTE] 
> Запись типа A не поддерживается. 

### <a name="create-the-cname-record"></a>Создание записи CNAME
Перейдите к поставщику DNS и добавьте запись CNAME для сопоставления домена с адресом <имя_службы>.azuremicroservices.io. Здесь <имя_службы> обозначает имя вашего экземпляра Azure Spring Cloud. Для этих целей поддерживаются подстановочные знаки в именах доменов и поддоменов. После добавления записи CNAME страница управления записями DNS будет выглядеть примерно так: 

![Страница записей DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Сопоставление личного домена с приложением Azure Spring Cloud
Если у вас нет приложения в Azure Spring Cloud, выполните инструкции, приведенные в статье [Краткое руководство. Запуск существующего приложения Azure Spring Cloud с помощью портала Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Перейдите к странице приложения.

1. Выберите **Личный домен**.
2. Теперь щелкните **Добавить личный домен**. 

    ![Личный домен](./media/custom-dns-tutorial/custom-domain.png)

3. Введите полное доменное имя, для которого вы добавили запись CNAME, например www.contoso.com. Убедитесь, что для параметра Hostname указан тип записи CNAME (<имя_службы>.azuremicroservices.io)
4. Щелкните **Проверить**, чтобы активировать кнопку **Добавить**.
5. Нажмите кнопку **Добавить**.

    ![Добавление личного домена](./media/custom-dns-tutorial/add-custom-domain.png)

Вы также можете использовать Azure CLI для добавления личного домена.
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

У одного приложения может быть несколько доменов, но каждый домен может быть сопоставлен только с одним приложением. После успешного сопоставления личного домена с приложением вы увидите его в таблице личных доменов.

![Таблица личных доменов](./media/custom-dns-tutorial/custom-domain-table.png)

Вы также можете использовать Azure CLI для отображения списка личных доменов.
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> Метка **Небезопасно** для личного домена означает, что он еще не привязан к SSL-сертификату. На любой HTTPS-запрос из браузера к такому личному домену возвращается сообщение об ошибке или предупреждение.

## <a name="add-ssl-binding"></a>Добавление привязки SSL
В таблице личных доменов выберите **Добавить SSL-привязку**, как показано на предыдущем рисунке.  
1. В поле **Сертификат** выберите или импортируйте сертификат.
1. Выберите команду **Сохранить**.

    ![Добавление привязки SSL](./media/custom-dns-tutorial/add-ssl-binding.png)

Вы также можете использовать Azure CLI, чтобы **добавить привязки SSL**.
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

После успешного добавления привязки SSL состояние домена станет "безопасным": **Работоспособно**. 

![Добавление привязки SSL](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Принудительное использование HTTPS
По умолчанию любой пользователь по-прежнему может обращаться к приложению по протоколу HTTP, но вы можете перенаправить все HTTP-запросы на порт HTTPS.

Для этого на странице приложения в области навигации слева выберите **Личный домен**. Здесь для параметра **Только HTTPS** установите значение *True*.

![Добавление привязки SSL](./media/custom-dns-tutorial/enforce-http.png)

Вы также можете использовать Azure CLI для принудительного применения HTTPS.
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

По завершении операции перейдите по любому из URL-адресов HTTPS, которые указывают на ваше приложение. Обратите внимание, что URL-адреса HTTP теперь не работают.

## <a name="see-also"></a>См. также раздел
* [Что такое хранилище ключей Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Импорт сертификата](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Запуск приложения Spring Cloud с помощью Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

