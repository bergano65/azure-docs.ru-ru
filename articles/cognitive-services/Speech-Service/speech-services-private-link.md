---
title: Использование речевых служб с частными конечными точками
titleSuffix: Azure Cognitive Services
description: Практическое руководство. Использование речевых служб с частными конечными точками, предоставляемыми частной ссылкой Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: alexeyo
ms.openlocfilehash: 01a0171ed2b660fbabebf4276a74f8a3ea631bde
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516533"
---
# <a name="using-speech-services-with-private-endpoints-provided-by-azure-private-link"></a>Использование речевых служб с частными конечными точками, предоставляемыми частной ссылкой Azure

[Частная ссылка Azure](../../private-link/private-link-overview.md) позволяет подключаться к различным службам PaaS в Azure с помощью [частной конечной точки](../../private-link/private-endpoint-overview.md). Частная конечная точка — это частный IP-адрес в определенной [виртуальной сети](../../virtual-network/virtual-networks-overview.md) и подсети.

В этой статье объясняется, как настроить и использовать закрытые ссылки и частные конечные точки с помощью служб распознавания речи Azure. 

> [!NOTE]
> В этой статье описываются особенности настройки и использования частной связи с помощью служб распознавания речи Azure. Прежде чем продолжить, ознакомьтесь со статьей общие сведения об [использовании виртуальных сетей с Cognitive Services](../cognitive-services-virtual-networks.md).

Для включения речевого ресурса в сценариях частной конечной точки необходимо выполнить следующие задачи:
- [Создание пользовательского доменного имени ресурса речи](#create-custom-domain-name)
- [Создание и настройка частных конечных точек](#enabling-private-endpoints)
- [Настройка существующих приложений и решений](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Если позже вы решили удалить все частные конечные точки, но продолжать использовать ресурс, необходимые действия описаны в [этом разделе](#using-speech-resource-with-custom-domain-name-without-private-endpoints).

## <a name="create-custom-domain-name"></a>Создание имени пользовательского домена

Частные конечные точки нуждаются в использовании [Cognitive Services пользовательских поддоменных имен](../cognitive-services-custom-subdomains.md). Используйте приведенные ниже инструкции, чтобы создать его для своего речевого ресурса.

> [!WARNING]
> В речевом ресурсе с включенным пользовательским именем домена используется другой способ взаимодействия со службами речи. Скорее всего, вам придется настроить код приложения как для [частных конечных точек](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) , так и для сценариев с [  поддержкой закрытых конечных точек](#using-speech-resource-with-custom-domain-name-without-private-endpoints) .
>
> Операция включения пользовательского доменного имени [**необратима**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). Единственный способ вернуться к [региональному имени](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) — создать новый речевой ресурс. 
>
> Особенно в случаях, когда у вашего речевого ресурса много связанных пользовательских моделей и проектов, созданных с помощью [Speech Studio](https://speech.microsoft.com/) , **настоятельно** рекомендуется выполнить настройку с помощью тестового ресурса, а затем изменить только ту, которая использовалась в рабочей среде.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

- Перейдите по адресу [портал Azure](https://portal.azure.com/) и войдите в учетную запись Azure.
- Выбор требуемого ресурса речи
- Выбор *сети* (группа *управления ресурсами* ) 
- На вкладке " *брандмауэры и виртуальные сети* " (по умолчанию) нажмите кнопку " **создать имя пользовательского домена** ".
- Появится новая панель с инструкциями по созданию уникального пользовательского поддомена для ресурса.
> [!WARNING]
> После создания имени пользовательского домена его **нельзя** изменить. Дополнительные сведения см. в приведенном выше предупреждении.
- После завершения операции может потребоваться выбрать *ключи и конечную точку* (группа *управления ресурсами* ) и проверить новое имя конечной точки ресурса в формате <p />`{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Для работы с этим разделом необходимо локально запустить PowerShell версии 7. x или более поздней версии с Azure PowerShellным модулем 5.1.0 или более поздней версией. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

Прежде чем продолжить выполнение `Connect-AzAccount` , создайте подключение к Azure.

## <a name="verify-custom-domain-name-availability"></a>Проверка доступности имени пользовательского домена

Необходимо проверить, является ли личный домен, который вы хотите использовать, бесплатным. Мы будем использовать метод [проверки доступности домена](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) из Cognitive Services REST API. Ознакомьтесь с комментариями в блоке кода ниже, объясняя шаги.

> [!TIP]
> Приведенный ниже код **не** будет работать в Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
Set-AzContext -SubscriptionId $subId

# Preparing OAuth token which is used in request
# to Cognitive Services REST API
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Preparing and executing the request to Cognitive Services REST API
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Если требуемое имя доступно, вы получите ответ следующего вида:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Если имя уже используется, вы получите следующий ответ:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="enabling-custom-domain-name"></a>Включение пользовательского имени домена

Чтобы включить пользовательское доменное имя для выбранного ресурса речи, мы используем командлет [Set-азкогнитивесервицесаккаунт](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) . Ознакомьтесь с комментариями в блоке кода ниже, объясняя шаги.

> [!WARNING]
> После успешного выполнения приведенного ниже кода будет создано пользовательское доменное имя для своего ресурса речи. Это имя **нельзя** изменить. Дополнительные сведения см. в приведенном выше предупреждении.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource
# WARNING! THIS IS NOT REVERSIBLE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Для работы с этим разделом требуется последняя версия Azure CLI. Если вы используете Azure Cloud Shell, последняя версия уже установлена.

## <a name="verify-custom-domain-name-availability"></a>Проверка доступности имени пользовательского домена

Необходимо проверить, является ли личный домен, который вы хотите использовать, бесплатным. Мы будем использовать метод [проверки доступности домена](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) из Cognitive Services REST API. 

Скопируйте приведенный ниже блок кода, вставьте имя пользовательского домена и сохраните его в файл `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Скопируйте файл в текущую папку или отправьте его в Azure Cloud Shell и выполните следующую команду. (Замените `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` идентификатором подписки Azure).

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Если требуемое имя доступно, вы получите ответ следующего вида:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Если имя уже используется, вы получите следующий ответ:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enabling-custom-domain-name"></a>Включение пользовательского имени домена

Чтобы включить пользовательское доменное имя для выбранного ресурса речи, мы используем команду [AZ cognitiveservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Выберите подписку Azure, содержащую речевой ресурс. Если ваша учетная запись Azure имеет только одну активную подписку, этот шаг можно пропустить. (Замените `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` идентификатором подписки Azure).
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Задайте имя пользовательского домена для выбранного ресурса. Замените примеры значений параметров на фактические и выполните приведенную ниже команду.
> [!WARNING]
> После успешного выполнения приведенной ниже команды вы создадите пользовательское доменное имя для своего ресурса речи. Это имя **нельзя** изменить. Дополнительные сведения см. в приведенном выше предупреждении.
```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enabling-private-endpoints"></a>Включение частных конечных точек

Включите закрытую конечную точку с помощью портал Azure, Azure PowerShell или Azure CLI.

Мы рекомендуем использовать [закрытую зону DNS](../../dns/private-dns-overview.md) , подключенную к виртуальной сети, с необходимыми обновлениями для частных конечных точек, которые мы создаем по умолчанию в процессе подготовки. Однако при использовании собственного DNS-сервера может потребоваться внести дополнительные изменения в конфигурацию DNS. См. раздел [DNS для частных конечных точек](#dns-for-private-endpoints) . Лучше всего выбрать стратегию DNS _ *перед* подготовкой частных конечных точек для рабочего речевого ресурса. Также рекомендуется предварительное тестирование, особенно если используется собственный DNS-сервер.

Используйте следующие статьи для создания частных конечных точек. Статьи используют веб-приложение как образец ресурса для включения в частные конечные точки. Вместо этого используйте следующие параметры:

| Параметр             | Значение                                    |
|---------------------|------------------------------------------|
| Тип ресурса       | **Microsoft. CognitiveServices/учетные записи** |
| Ресурс            | **\<your-speech-resource-name>**         |
| Целевой подресурс | **учетная запись**                              |

- [Создание частной конечной точки с помощью портала Azure](../../private-link/create-private-endpoint-portal.md)
- [Создание частной конечной точки с помощью Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Создание частной конечной точки с помощью Azure CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="dns-for-private-endpoints"></a>DNS для частных конечных точек

Ознакомьтесь с общими принципами [DNS для частных конечных точек в Cognitive Servicesных ресурсах](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Затем убедитесь, что конфигурация DNS работает правильно (см. следующие подразделы).

#### <a name="mandatory-check-dns-resolution-from-the-virtual-network"></a>(Обязательная проверка). Разрешение DNS из виртуальной сети

`my-private-link-speech.cognitiveservices.azure.com`Для этого раздела мы будем использовать в качестве примера DNS-имя ресурса речи.

Войдите в виртуальную машину, расположенную в виртуальной сети, к которой присоединена частная конечная точка. Откройте командную строку Windows или оболочку Bash, выполните команду "nslookup" и убедитесь, что имя личного домена ресурса успешно разрешается:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```
Убедитесь, что разрешенный IP-адрес соответствует адресу частной конечной точки.

#### <a name="optional-check-dns-resolution-from-other-networks"></a>(Необязательная проверка). Разрешение DNS из других сетей

Эта проверка необходима, если вы планируете использовать свой речевой ресурс с поддержкой закрытой конечной точки в гибридном режиме, в котором включены *все сети* или *Выбранные сети и параметры доступа к частным конечным точкам* в разделе " *Сетевые подключения* " ресурса. Если планируется доступ к ресурсу только с помощью частной конечной точки, этот раздел можно пропустить.

`my-private-link-speech.cognitiveservices.azure.com`Для этого раздела мы будем использовать в качестве примера DNS-имя ресурса речи.

На любом компьютере, подключенном к сети, из которой вы разрешаете доступ к ресурсу открыть командную строку Windows или оболочку Bash, выполните команду nslookup и убедитесь, что имя личного домена ресурса успешно разрешается:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
Address:  13.69.67.71
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          my-private-link-speech.privatelink.cognitiveservices.azure.com
          westeurope.prod.vnet.cog.trafficmanager.net
```

Обратите внимание, что разрешенные IP-адреса указывают на конечную точку прокси виртуальной сети, которая используется для диспетчеризации сетевого трафика в закрытую конечную точку с включенным Cognitive Services ресурсом. Это поведение будет отличаться для ресурса с включенным именем личного домена, но *без* настроенных частных конечных точек. См. [Этот раздел](#dns-configuration).

## <a name="adjusting-existing-applications-and-solutions"></a>Настройка существующих приложений и решений 

Речевой ресурс с включенным пользовательским доменом использует другой способ взаимодействия со службами речи. Это справедливо для ресурса речи, поддерживающего пользовательский домен [, как с](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) закрытыми конечными точками, так и [без](#using-speech-resource-with-custom-domain-name-without-private-endpoints) них. В текущем разделе содержатся сведения, необходимые для обоих вариантов.

### <a name="using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Использование речевого ресурса с включенным пользовательским именем домена и частной конечной точкой

В речевом ресурсе с включенным пользовательским именем домена и частной конечной точкой используется другой способ взаимодействия со службами речи. В этом разделе объясняется, как использовать такой ресурс с речевыми службами REST API и [речевым пакетом SDK](speech-sdk.md).

> [!NOTE]
> Обратите внимание, что речевой ресурс без закрытых конечных точек, но с включенным **пользовательским именем домена** также имеет особый способ взаимодействия со службами речи, но этот способ отличается от сценария для речевого ресурса с поддержкой частной конечной точки. Если у вас есть такой ресурс (скажем, у вас есть ресурс с частными конечными точками, но затем решили удалить их), ознакомьтесь с [разделом «корреспондент](#using-speech-resource-with-custom-domain-name-without-private-endpoints)».

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Речевой ресурс с пользовательским именем домена и частной конечной точкой. Использование с REST API

`my-private-link-speech.cognitiveservices.azure.com`Для этого раздела мы будем использовать в качестве примера DNS-имя ресурса речи (личный домен).

##### <a name="note-on-speech-services-rest-api"></a>Примечание о службах речи REST API

Службы речи имеют REST API для преобразования [речи в текст](rest-speech-to-text.md) и преобразования [текста в речь](rest-text-to-speech.md). Для сценария с включенной частной конечной точкой необходимо учитывать следующее.

Речь-to-Text имеет два разных интерфейса API-интерфейсов RESTFUL. Каждый API-интерфейс предназначен для разных целей, использует разные конечные точки и требует другого подхода при использовании индивидуального сценария с включенной конечной точкой.

API-интерфейсы RESTFUL для преобразования речи в текст:
- Преобразование [речи в текст REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) используется для [пакетной](batch-transcription.md) записи и [пользовательское распознавание речи](custom-speech-overview.md). Версия 3.0 является [последователем версии 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- [REST API преобразования речи в текст для коротких аудиофайлов](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) используется для интерактивного разговора. 

Использование преобразования речи в REST API текст для коротких аудио-и речевых REST API в сценарии частной конечной точки аналогично тому, что аналогично [случаю речевого пакета SDK](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) , описанному далее в этой статье. 

Преобразование речи в текст REST API v 3.0 использует другой набор конечных точек, поэтому для сценария с включенной конечной точкой требуется другой подход.

Оба варианта описаны в следующих подразделах.


##### <a name="speech-to-text-rest-api-v30"></a>Преобразование речи в текст REST API v 3.0

Обычно речевые ресурсы используют [Cognitive Services региональных конечных точек](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) для взаимодействия с [текстом REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Эти ресурсы имеют следующий формат именования: <p/>`{region}.api.cognitive.microsoft.com`

Вот пример URL-адреса запроса:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
После включения пользовательского домена для речевого ресурса (который необходим для частных конечных точек) такой ресурс будет использовать следующий шаблон DNS-имени для базовой конечной точки REST API: <p/>`{your custom name}.cognitiveservices.azure.com`

Это означает, что в нашем примере имя конечной точки REST API будет следующим: <p/>`my-private-link-speech.cognitiveservices.azure.com`

А пример URL-адреса запроса выше должен быть преобразован в:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Этот URL-адрес должен быть доступен из виртуальной сети с подключенной частной конечной точкой (при условии, что [разрешение DNS указано правильно](#mandatory-check-dns-resolution-from-the-virtual-network)).

Поэтому, как правило, после включения пользовательского доменного имени для речевого ресурса необходимо заменить имя узла во всех URL-адресах запроса на новое имя узла пользовательского домена. Все остальные части запроса (например, путь в приведенном `/speechtotext/v3.0/transcriptions` выше примере) остаются неизменными.

> [!TIP]
> Некоторые клиенты разработали приложения, которые используют регион региона DNS-имени региональной конечной точки (например, для отправки запроса в речевой ресурс, развернутый в определенном регионе Azure).
>
> Пользовательское доменное имя ресурса речи **не** содержит сведений о регионе, в котором развернут ресурс. Поэтому описанная выше логика приложения **не** будет работать и должна быть изменена.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>REST API преобразования речи в текст для коротких REST API аудио и текста в речь

[REST API преобразования речи в текст для коротких аудио](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) и [текста в речь REST API](rest-text-to-speech.md) использовать два типа конечных точек:
- [Cognitive Services региональные конечные точки](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) для взаимодействия с Cognitive Services REST API для получения маркера авторизации
- Специальные конечные точки для всех других операций

Подробное описание специальных конечных точек и их URL-адресов, которые должны быть преобразованы для ресурса речи с поддержкой частной конечной точки, приведены в [этом подразделе](#general-principle) раздела "использование с РЕЧЕВым пакетом SDK" ниже. Тот же принцип, который описан для пакета SDK, относится к REST API преобразования речи в текст v 1.0 и преобразования текста в речь.

Ознакомьтесь с материалами в подразделе, упомянутом в предыдущем абзаце, и см. Следующий пример. (В примере описывается REST API преобразования текста в речь; использование REST API преобразования речи в текст для коротких аудиозаписей полностью эквивалентно)

> [!NOTE]
> При использовании **REST API речи в текст для коротких аудио** в сценариях частных конечных точек необходимо использовать токен авторизации, [переданный](rest-speech-to-text.md#request-headers) в `Authorization` [заголовке](rest-speech-to-text.md#request-headers); передача ключа подписки речи в специальную конечную точку через `Ocp-Apim-Subscription-Key` заголовок **не** будет работать и создаст ошибку 401.

**Пример использования преобразования текста в речь REST API.**

В качестве примера региона Azure мы будем использовать Западная Европа, а в `my-private-link-speech.cognitiveservices.azure.com` качестве примера имя DNS-имени ресурса речи (личный домен). Пользовательское доменное имя `my-private-link-speech.cognitiveservices.azure.com` в нашем примере принадлежит к речевому ресурсу, созданному в Западной Европе.

Чтобы получить список голосов, поддерживаемых в регионе, один из них должен выполнить следующие две операции:

- Получение маркера авторизации через
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Использование полученного маркера получение списка голосов через
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(См. Дополнительные сведения о действиях, описанных выше в разделе Преобразование [текста в речь REST API документации](rest-text-to-speech.md)).

Для ресурса речи с включенной частной конечной точкой URL-адреса конечных точек для одной и той же последовательности операций необходимо изменить. Та же последовательность будет выглядеть следующим образом:
- Получение маркера авторизации через
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(см. подробное объяснение в подразделе " [речь-текст REST API v 3.0](#speech-to-text-rest-api-v30) " выше)
- Использование полученного маркера получение списка голосов через
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(см. подробное описание в подразделе [общий принцип](#general-principle) раздела "использование с помощью РЕЧЕВОГО пакета SDK" ниже).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Речевой ресурс с пользовательским именем домена и частной конечной точкой. Использование с речевым пакетом SDK

Для использования речевого пакета SDK с пользовательским именем домена и речевыми ресурсами с поддержкой частных конечных точек требуется проверка и вероятные изменения кода приложения. Мы работаем над более простой поддержкой сценария частной конечной точки.

`my-private-link-speech.cognitiveservices.azure.com`Для этого раздела мы будем использовать в качестве примера DNS-имя ресурса речи (личный домен).

##### <a name="general-principle"></a>Общий принцип

Обычно в сценариях пакета SDK (а также в сценариях REST API преобразования текста в речь) для речевых ресурсов используются специальные региональные конечные точки для различных предложений услуг. Для этих конечных точек используется формат DNS-имен: </p>`{region}.{speech service offering}.speech.microsoft.com`

Пример </p>`westeurope.stt.speech.microsoft.com`

Все возможные значения для региона (первый элемент DNS-имени [) перечислены ниже в следующей таблице](regions.md) представлены возможные значения для предложения служб речи (второй элемент DNS-имени):

| Значение DNS-имени | Предложение по голосовым службам                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Пользовательские команды](custom-commands.md)                       |
| `convai`       | [Транскрибирование бесед](conversation-transcription.md) |
| `s2s`          | [Перевод речи](speech-translation.md)                 |
| `stt`          | [Преобразование речи в текст](speech-to-text.md)                         |
| `tts`          | [Преобразование текста в речь](text-to-speech.md)                         |
| `voice`        | [Custom Voice](how-to-custom-voice.md) (Пользовательские голосовые модели)                      |

Таким образом, приведенный выше пример ( `westeurope.stt.speech.microsoft.com` ) означает конечную точку преобразования речи в текст в Западной Европе.

Конечные точки с поддержкой частных конечных точек взаимодействуют с голосовыми службами через специальный прокси-сервер, так как **URL-адреса подключения к конечной точке должны быть изменены**. Применяется следующий принцип: "Стандартный" URL-адрес конечной точки соответствует шаблону <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Его следует изменить на: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Пример 1.** Приложение взаимодействует с использованием следующего URL-адреса (распознавание речи с использованием базовой модели для английского языка США в Западной Европе): 
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Чтобы использовать его в сценарии с включенной частной конечной точкой, когда пользовательское доменное имя ресурса речи — `my-private-link-speech.cognitiveservices.azure.com` этот URL-адрес необходимо изменить следующим образом:
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Рассмотрим более подробно:
- Имя узла `westeurope.stt.speech.microsoft.com` заменено именем узла пользовательского домена `my-private-link-speech.cognitiveservices.azure.com`
- Второй элемент исходного DNS-имени ( `stt` ) становится первым элементом URL-пути и предшествует исходному пути, то есть исходный URL-адрес `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` становится `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`
 
**Пример 2.** Приложение взаимодействует с использованием следующего URL-адреса (синтезатор речи с помощью пользовательской голосовой модели в Западной Европе): 
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```
Чтобы использовать его в сценарии с включенной частной конечной точкой, когда пользовательское доменное имя ресурса речи — `my-private-link-speech.cognitiveservices.azure.com` этот URL-адрес необходимо изменить следующим образом: 
```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Применяется тот же принцип, что и в примере 1, но элемент key на данный момент равен `voice` .

##### <a name="modifying-applications"></a>Изменение приложений

Чтобы применить принцип, описанный в предыдущем разделе, к коду приложения, необходимо выполнить два основных действия.

- Определение URL-адреса конечной точки, используемого приложением
- Измените URL-адрес конечной точки, как описано в предыдущем разделе, и создайте `SpeechConfig` экземпляр класса с использованием этого измененного URL-адреса явным образом.

###### <a name="determining-application-endpoint-url"></a>Определение URL-адреса конечной точки приложения

- [Включение ведения журнала для приложения](how-to-use-logging.md) и его запуск для создания журнала
- В файле журнала найдите `SPEECH-ConnectionUrl` . Строка будет содержать `value` параметр, который, в свою очередь, будет содержать полный URL-адрес, используемый приложением.

Пример строки файла журнала с URL-адресом конечной точки:
```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```
Таким образом, URL-адрес, используемый приложением в этом примере:
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
###### <a name="creating-speechconfig-instance-using-full-endpoint-url"></a>Создание `SpeechConfig` экземпляра с использованием полного URL-адреса конечной точки

Измените конечную точку, определенную в предыдущем разделе, как описано в [общем принципе](#general-principle) .

Теперь необходимо изменить способ создания экземпляра `SpeechConfig` . Скорее всего, наше приложение использует примерно следующий результат:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Это не будет работать для речевого ресурса с поддержкой частной конечной точки из-за изменений имени узла и URL, описанных в предыдущих разделах. Если вы попытаетесь запустить существующее приложение без каких-либо изменений, используя ключ ресурса с включенной частной конечной точкой, будет выводится сообщение об ошибке проверки подлинности (401).

Чтобы сделать его работоспособным, необходимо изменить способ создания экземпляра `SpeechConfig` класса и использовать инициализацию "из конечной точки"/"с конечной точкой". Предположим, что определены следующие две переменные:
- `subscriptionKey` содержит ключ ресурса речи с включенной частной конечной точкой
- `endPoint` содержит полный **измененный** URL-адрес конечной точки (с использованием типа, необходимого для языка программирования корреспондента). В нашем примере эта переменная должна содержать
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
Затем необходимо создать экземпляр класса следующим `SpeechConfig` образом:
```csharp
var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
```
```cpp
auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
```
```java
SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
```
> [!TIP]
> Параметры запроса, указанные в URI конечной точки, не изменяются, даже если они задаются любыми другими API. Например, если язык распознавания определен в URI как параметр запроса "Language = en-US" и для него также задано значение "ru-RU" через свойство "корреспондент", то параметр языка в URI имеет приоритет, а действующий язык — "en-US". Другие API могут устанавливать только параметры, которые не указаны в URI конечной точки.

После такого изменения приложение должно работать с закрытыми речевыми ресурсами. Мы работаем над более простой поддержкой сценария частной конечной точки.

### <a name="using-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Использование речевого ресурса с именем пользовательского домена без закрытых конечных точек

В этой статье мы указали несколько раз, что включение личного домена для речевого ресурса является **необратимым** , и такой ресурс будет использовать другой способ связи с голосовыми службами, сравнивая с обычными (то есть, использующими [региональные) именами конечных точек](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

В этом разделе описывается использование речевого ресурса с включенным пользовательским именем домена, но **без** частных конечных точек с голосовыми службами REST API и [речевого пакета SDK](speech-sdk.md). Это может быть ресурс, который был бы использован в сценарии частной конечной точки, но в нем были удалены частные конечные точки.

#### <a name="dns-configuration"></a>Настройка DNS

Помните, как DNS-имя личного домена с включенным голосовым ресурсом закрытой конечной точки [разрешается из общедоступных сетей](#optional-check-dns-resolution-from-other-networks). В этом случае IP-адрес разрешается в конечную точку прокси виртуальной сети, которая используется для диспетчеризации сетевого трафика в закрытую конечную точку с включенным Cognitive Services ресурсом.

Однако при удалении **всех** частных конечных точек ресурсов (или сразу после включения имени личного домена) запись CNAME для речевого ресурса повторно подготавливается, а теперь указывает на IP-адрес корреспондента [Cognitive Services региональная конечная точка](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Поэтому выходные данные `nslookup` команды будут выглядеть следующим образом:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Сравните его с выходными данными [этого раздела](#optional-check-dns-resolution-from-other-networks).

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Речевой ресурс с именем личного домена без закрытых конечных точек. Использование с REST API

##### <a name="speech-to-text-rest-api-v30"></a>Преобразование речи в текст REST API v 3.0

Использование преобразования речи в текст REST API v 3.0 полностью эквивалентно случаю [речевых ресурсов с поддержкой частных конечных точек](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>REST API преобразования речи в текст для коротких REST API аудио и текста в речь

В этом случае речь-текст REST API для короткого аудио и текста в речь, REST API использование не имеет различия в общем случае с одним исключением для REST API короткого звучания (см. Примечание ниже). Оба API-интерфейса следует использовать, как описано в разделе Преобразование [речи в текст REST API для коротких аудио](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) и [текста в речь REST API](rest-text-to-speech.md) документации.

> [!NOTE]
> При использовании **REST API преобразования речи в текст для коротких звуков** в пользовательских сценариях необходимо использовать токен авторизации, [передаваемый по](rest-speech-to-text.md#request-headers) `Authorization` [заголовку](rest-speech-to-text.md#request-headers); передача ключа подписки речи в специальную конечную точку через `Ocp-Apim-Subscription-Key` заголовок **не** будет работать и создаст ошибку 401.

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Речевой ресурс с именем личного домена без закрытых конечных точек. Использование с речевым пакетом SDK

Использование речевого пакета SDK с включенными пользовательскими доменными именами **без** закрытых конечных точек требует просмотра и, вероятнее всего, изменений кода приложения. Обратите внимание, что эти изменения **различны** в случае использования [речевого ресурса с поддержкой частной конечной точки](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). Мы работаем над более простой поддержкой сценария частной конечной точки или пользовательского домена.

`my-private-link-speech.cognitiveservices.azure.com`Для этого раздела мы будем использовать в качестве примера DNS-имя ресурса речи (личный домен).

В разделе о [речевом ресурсе с поддержкой частных конечных точек](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) мы объяснили, как определить используемый URL-адрес конечной точки, изменить его и заставить его работать с помощью инициализации экземпляра класса из конечной точки или с конечной точкой `SpeechConfig` .

Однако если вы попытаетесь запустить то же приложение после удаления всех частных конечных точек (что позволяет некоторое время повторной подготовке записи DNS-корреспондента), вы получите внутреннюю ошибку службы (404). Причина — [запись DNS](#dns-configuration) , которая теперь указывает на региональную конечную точку Cognitive Services, а не на прокси-сервер виртуальной сети, и URL-пути, такие как `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` , не будут найдены, поэтому ошибка "не найдена" (404).

При «откате» приложения к стандартному экземпляру `SpeechConfig` в стиле
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
приложение завершится с ошибкой проверки подлинности (401).

##### <a name="modifying-applications"></a>Изменение приложений

Чтобы включить в приложении сценарий речевого ресурса с именем личного домена без закрытых конечных точек, необходимо выполнить следующие действия.
- Запрос маркера авторизации через Cognitive Services REST API
- Создание экземпляра `SpeechConfig` класса с использованием метода "from" маркера авторизации "/" с токеном авторизации " 

###### <a name="requesting-authorization-token"></a>Запрос маркера авторизации

Сведения о получении маркера с помощью REST API Cognitive Services см. в [этой статье](../authentication.md#authenticate-with-an-authentication-token) . 

Используйте имя личного домена в URL-адресе конечной точки, в нашем примере это URL-адрес:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Этот URL-адрес можно найти в разделе *ключи и конечная точка* (группа *управления ресурсами* ) вашего ресурса речи в портал Azure.

###### <a name="creating-speechconfig-instance-using-authorization-token"></a>Создание `SpeechConfig` экземпляра с помощью токена авторизации

Необходимо создать экземпляр `SpeechConfig` класса с помощью маркера авторизации, полученного в предыдущем разделе. Предположим, что определены следующие переменные:

- `token` содержащий маркер авторизации, полученный в предыдущем разделе
- `azureRegion` содержит имя [области](regions.md) ресурсов речи (пример: `westeurope` )
- `outError` (только для случая [с целевым](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) вариантом C)

Затем необходимо создать экземпляр класса следующим `SpeechConfig` образом:
```csharp
var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
```
```cpp
auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
```
```java
SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
```
> [!NOTE]
> Вызывающий объект должен проверить допустимость маркера авторизации. До истечения срока действия маркера авторизации вызывающий объект должен обновить его, вызвав этот метод задания с новым действительным токеном. При копировании значений конфигурации при создании нового распознавателя или синтезатора новое значение маркера не будет применяться к уже созданным распознавателям. Для распознавателей или синтезаторов, созданных ранее, необходимо задать маркер авторизации соответствующего распознавателя или синтезатора, чтобы обновить маркер. В противном случае распознаватели или синтезаторы будут столкнуться с ошибками во время распознавания или синтеза.

После этого приложение должно работать с пользовательскими доменными ресурсами с включенными голосовыми данными без закрытых конечных точек. Мы работаем над более удобной поддержкой пользовательского домена/частной конечной точки.

## <a name="pricing"></a>Цены

Дополнительные сведения о ценах см. на [странице цен на службу "Приватный канал" Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о службе [Приватный канал Azure](../../private-link/private-link-overview.md)
* Дополнительные сведения о [пакете SDK для распознавания речи](speech-sdk.md)
* Дополнительные сведения о [REST API преобразования речи в текст](rest-speech-to-text.md)
* Дополнительные сведения о [REST API преобразования текста в речь](rest-text-to-speech.md)
