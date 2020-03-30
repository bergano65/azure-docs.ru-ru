---
title: Перенаправление проблем на URL-адрес службы app
titleSuffix: Azure Application Gateway
description: В этой статье приводится информация о том, как устранить проблему перенаправления при использовании шлюза приложений Azure с помощью службы приложений Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293540"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Проблемы службы приложения в шлюзе приложения

Узнайте, как диагностировать и устранять проблемы, с которыми вы можете столкнуться при использовании службы приложений Azure в качестве резервной цели с помощью шлюза приложений Azure.

## <a name="overview"></a>Обзор

В этой статье вы узнаете, как устранить следующие проблемы:

> [!div class="checklist"]
> * URL-адрес службы приложений разоблачается в браузере при перенаправлении.
> * Домен cookie-файлов службы приложений ARRAffinity устанавливается на имя хоста то, example.azurewebsites.net, а не на исходный хост.

Когда приложение бэк-энда отправляет ответ на перенаправление, может потребоваться перенаправить клиента на другой URL-адрес, чем тот, который указан в приложении бэк-энда. Вы можете сделать это, когда служба приложения размещается за шлюзом приложения и требует от клиента перенаправить на свой относительный путь. Примером является перенаправление с contoso.azurewebsites.net/path1 на contoso.azurewebsites.net/path2. 

Когда служба приложения отправляет ответ на перенаправление, она использует то же имя хоста в заголовке места своего ответа, что и запрос в запросе, который он получает от шлюза приложения. Например, клиент делает запрос непосредственно contoso.azurewebsites.net/path2 вместо того, чтобы пройти через шлюз приложения contoso.com/path2. Вы не хотите обходить шлюз приложения.

Эта проблема может возникнуть по следующим основным причинам:

- Перенаправление настроено на службу приложений. Перенаправление может быть так же просто, как добавление задней слэйс к запросу.
- У вас есть активная проверка каталога Azure, которая вызывает перенаправление.

Кроме того, при использовании служб приложений за шлюзом приложения доменное имя, связанное с шлюзом приложения (example.com), отличается от доменного имени службы приложения (скажем, example.azurewebsites.net). Значение домена для cookie ARRAffinity, установленное службой приложения, содержит example.azurewebsites.net доменное имя, что нежелательно. Первоначальное имя хоста, example.com, должно быть значением доменного имени в файле cookie.

## <a name="sample-configuration"></a>Пример конфигурации

- HTTP слушатель: Основной или многофункциональный
- Пул адресов бэк-энда: Служба приложений
- Настройки HTTP: **Выберите хостимя из backend Адрес** включен
- Зонд: **Выберите хостимя из включенных настроек HTTP**

## <a name="cause"></a>Причина

Служба приложений — это мультитенантная служба, поэтому она использует заголовок хоста в запросе для направления запроса в правильную конечную точку. Доменное имя Службы app Services по умолчанию, azurewebsites.net (скажем, contoso.azurewebsites.net), отличается от доменного имени шлюза приложения (скажем, contoso.com). 

Первоначальный запрос от клиента имеет доменное имя шлюза приложения, contoso.com, как имя хоста. Необходимо настроить шлюз приложения, чтобы изменить имя хоста в первоначальном запросе на имя хостатора службы приложения, когда он направляет запрос в задний конец службы приложения. Используйте переключатель **Pick Hostname из Backend Address** в конфигурации настройки HTTP прицельного ключа приложения. Используйте переключатель **Pick Hostname из настроек Backend HTTP** в конфигурации зонда работоспособности.



![Шлюз приложения меняет имя хоста](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Когда служба приложения выполняет перенаправление, она использует переочерденное имя узла contoso.azurewebsites.net в заголовке местоположения вместо первоначального имени узла contoso.com, если не настроениначе иначе. Проверьте следующие заголовки запросов и ответов.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
В предыдущем примере обратите внимание, что заголовок ответа имеет код статуса 301 для перенаправления. Заголовок местоположения имеет имя хостатора службы `www.contoso.com`приложения вместо первоначального имени хоста.

## <a name="solution-rewrite-the-location-header"></a>Решение: Перезаписать заголовок местоположения

Установите имя хоста в заголовке местоположения к доменному имени шлюза приложения. Для этого создайте [правило переписывания](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) с условием, которое оценивает, содержит ли заголовок местоположения в ответе azurewebsites.net. Он также должен выполнить действие, чтобы переписать заголовок местоположения, чтобы иметь имя хоста шлюза приложения. Для получения дополнительной информации смотрите инструкции о [том, как переписать заголовок местоположения.](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)

> [!NOTE]
> Поддержка перезаписи заголовка HTTP доступна только для [Standard_v2 и WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) Gateway приложений. Если вы используете v1 SKU, мы рекомендуем [вам мигрировать с v1 на v2.](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) Вы хотите использовать переписать и другие [расширенные возможности,](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) которые доступны с v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Альтернативное решение: Используйте пользовательское доменное имя

Если вы используете v1 SKU, вы не можете переписать заголовок местоположения. Эта возможность доступна только для v2 SKU. Чтобы решить проблему перенаправления, передайте в службу приложения то же имя, которое получает шлюз приложения, вместо того, чтобы переопределить хост.

Служба приложений теперь делает перенаправление (если таковой имеется) на том же исходном заголовке, который указывает на шлюз приложения, а не на его собственный.

Вы должны владеть пользовательским доменом и следовать этому процессу:

- Зарегистрируйте домен в пользовательском списке доменов службы приложений. В пользовательском домене должен быть CNAME, который указывает на F-DN службы приложений. Для получения дополнительной информации смотрите [карту существующего пользовательского имени DNS для службы приложений Azure.](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)

    ![Пользовательский список доменов службы приложений](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Служба приложений готова принять имя `www.contoso.com`хоста. Измените запись CNAME в DNS, чтобы указать ее обратно на F-DN шлюза приложения, например, `appgw.eastus.cloudapp.azure.com`.

- Убедитесь, что `www.contoso.com` ваш домен решает с F-DN шлюза приложения, когда вы делаете запрос DNS.

- Установите пользовательский зонд, чтобы отключить **Pick Hostname от настроек Backend HTTP.** На портале Azure очистите флажок в настройках зонда. В PowerShell не используйте переключатель **-PickHostNameBackendHttpSettings** в команде **Set-AzApplicationGatewayProbeConfig.** В поле имени хоста зонда введите F-DN службы приложения, example.azurewebsites.net. Запросы зонда, отправленные из шлюза приложения, содержат этот F-DN в заголовке хоста.

  > [!NOTE]
  > Для следующего шага убедитесь, что пользовательский зонд не связан с настройками http-энда. В настройках HTTP в этой точке по-прежнему включен **переключатель Pick Hostname из Backend Address.**

- Установите настройки http шлюза приложения, чтобы отключить **Pick Hostname от Backend Address.** На портале Azure очистите флажок. В PowerShell не используйте коммутатор **-PickHostNameBackendAddress** в команде **Set-AzApplicationGatewayBackendHttpSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSettingSetting**

- Связать пользовательский зонд обратно в настройки бэк-энда HTTP, и убедитесь, что задний конец является здоровым.

- Шлюз приложения должен теперь `www.contoso.com`направить одно и то же имя хоста в службу приложения. Перенаправление происходит на одном и том же имени хоста. Проверьте следующие заголовки запросов и ответов.

Для реализации предыдущих шагов с помощью PowerShell для существующей настройки используйте следующий сценарий PowerShell. Обратите внимание, что мы не использовали коммутаторы **-PickHostname** в конфигурации параметров зонда и HTTP.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Дальнейшие действия

Если предыдущие шаги не решили проблему, откройте [билет поддержки.](https://azure.microsoft.com/support/options/)
