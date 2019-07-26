---
title: Устранение неполадок шлюза приложений Azure со службой приложений — перенаправление в URL-адрес службы приложений
description: В этой статье содержатся сведения о том, как устранить проблему перенаправления при использовании шлюза приложений Azure со службой приложений Azure.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347882"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Устранение неполадок службы приложений в шлюзе приложений

Узнайте, как диагностировать и устранять проблемы, возникающие при использовании сервера приложений в качестве серверного целевого объекта с помощью шлюза приложений.

## <a name="overview"></a>Обзор

Из этой статьи вы узнаете, как устранить следующие проблемы.

> [!div class="checklist"]
> * URL-адрес службы приложений, предоставляемый в браузере при перенаправлении
> * Домен cookie Арраффинити службы приложений задан как имя узла службы приложений (example.azurewebsites.net) вместо исходного узла.

Когда серверное приложение отправляет ответ перенаправления, может потребоваться перенаправить клиента на URL-адрес, отличный от того, который указан в серверном приложении. Например, это может потребоваться, если служба приложений размещена за шлюзом приложений и требует от клиента перенаправления к относительному пути. (Например, перенаправление с contoso.azurewebsites.net/path1 на contoso.azurewebsites.net/path2.) Когда служба приложений отправляет ответ перенаправления, она использует то же имя узла в заголовке Location своего ответа, что и в запросе, полученном от шлюза приложений. Поэтому клиент сделает запрос напрямую contoso.azurewebsites.net/path2, а не через шлюз приложений (contoso.com/path2). Обход шлюза приложений нежелательно.

Эта проблема может возникать по следующим основным причинам.

- У вас есть перенаправление, настроенное в службе приложений. Перенаправление может быть простым добавлением завершающей косой черты в запрос.
- У вас есть аутентификация Azure AD, которая вызывает перенаправление.

Кроме того, при использовании служб приложений за шлюзом приложений имя домена, связанное с шлюзом приложений (example.com), будет отличаться от доменного имени службы приложений (скажем, example.azurewebsites.net), так как вы заметите, что значение домена для файла cookie Арраффинити, заданное службой приложений, будет содержать нежелательное имя домена "example.azurewebsites.net". Исходное имя узла (example.com) должно быть значением доменного имени в файле cookie.

## <a name="sample-configuration"></a>Пример конфигурации

- Прослушиватель HTTP: Базовый или несколько сайтов
- Внутренний пул адресов: Служба приложений
- Параметры HTTP: "Выбор имени узла из серверного адреса" включено
- Датчики Включен параметр "выбрать имя узла из параметров HTTP"

## <a name="cause"></a>Причина:

Так как служба приложений является многоклиентской службой, она использует заголовок узла в запросе для маршрутизации запроса в правильную конечную точку. Однако доменное имя по умолчанию для служб приложений, *. azurewebsites.net (скажем, contoso.azurewebsites.net), отличается от доменного имени шлюза приложений (скажем, contoso.com). Поскольку исходный запрос клиента имеет доменное имя шлюза приложений (contoso.com) в качестве имени узла, необходимо настроить шлюз приложений таким образом, чтобы он изменил имя узла в исходном запросе на имя узла служб приложений, когда оно направляет запрос в Серверная часть службы приложений.  Это достигается с помощью параметра "выбрать имя узла из внутреннего адреса" в конфигурации параметров HTTP шлюза приложений и параметра "Выбор имени узла из серверных параметров HTTP" в конфигурации проверки работоспособности.



![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Из-за этого, когда служба приложений выполняет перенаправление, она использует переопределенное имя узла "contoso.azurewebsites.net" в заголовке Location вместо исходного имени узла "contoso.com", если не указано иное. Вы можете проверить приведенные ниже примеры заголовков запросов и ответов.
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
В приведенном выше примере можно заметить, что заголовок ответа имеет код состояния 301 для перенаправления, а заголовок location содержит имя узла службы приложений вместо исходного имени узла "www.contoso.com".

## <a name="solution-rewrite-the-location-header"></a>Решение: Перезаписать заголовок Location

Необходимо задать имя узла в заголовке Location в качестве имени домена шлюза приложений. Для этого создайте [правило перезаписи](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) с условием, которое оценивает, содержит ли заголовок Location в ответе azurewebsites.NET, и выполняет действие для перезаписи заголовка Location с именем узла шлюза приложения.  См. инструкции по [перезаписи заголовка Location](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Поддержка переопределения HTTP-заголовков доступна только для [SKU Standard_V2 и WAF_v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) шлюза приложений. Если вы используете номер SKU v1, мы настоятельно рекомендуем [выполнить миграцию с версии v1 на версию 2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) , чтобы использовать функцию перезаписи и другие [Расширенные возможности](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) , доступные в SKU v2.

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>Альтернативное решение: Использовать личный домен службы приложений вместо доменного имени по умолчанию

Если вы используете номер SKU v1, вы не сможете переписать заголовок Location, так как эта возможность доступна только для SKU версии 2. Таким образом, чтобы устранить проблему перенаправления, необходимо передать то же имя узла, которое шлюз приложений получает в службу приложений, а не выполнить переопределение узла.

После этого служба приложений выполнит перенаправление (если таковое имеется) в том же исходном заголовке узла, который указывает на шлюз приложений, а не на его собственную.

Для этого необходимо владеть пользовательским доменом и выполнить описанный ниже процесс.

- Зарегистрируйте домен в списке личный домен службы приложений. Для этого в пользовательском домене должна быть указана запись CNAME, указывающая на полное доменное имя службы приложений. Дополнительные сведения см. [в статье сопоставьте существующее настраиваемое DNS-имя со службой приложений Azure](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- После этого служба приложений готова принять имя узла "www.contoso.com". Теперь измените запись CNAME в DNS, чтобы она указывала на полное доменное имя шлюза приложений. Например, "appgw.eastus.cloudapp.azure.com".

- Убедитесь, что домен "www.contoso.com" разрешается в полное доменное имя шлюза приложений при выполнении запроса DNS.

- Настройте пользовательскую пробу, чтобы отключить параметр "выбрать имя узла из серверных параметров HTTP". Это можно сделать на портале, сняв флажок в параметрах зонда и в PowerShell, не используя параметр-Пиккхостнамефромбаккендхттпсеттингс в команде Set-Азаппликатионгатевайпробеконфиг. В поле имя узла зонда введите полное доменное имя службы приложений "example.azurewebsites.net", так как запросы пробы, отправленные из шлюза приложений, будут содержать это сообщение в заголовке узла.

  > [!NOTE]
  > При выполнении следующего шага убедитесь, что пользовательская проверка не связана с параметрами HTTP серверной части, так как в этом месте параметры HTTP по-прежнему имеют включенный параметр "выбрать имя узла из внутреннего адреса".

- Задайте параметры HTTP для шлюза приложений, чтобы отключить параметр "выбрать имя узла из серверного адреса". Это можно сделать на портале, сняв флажок и в PowerShell, не используя параметр-Пиккхостнамефромбаккендаддресс в команде Set-Азаппликатионгатевайбаккендхттпсеттингс.

- Свяжите пользовательскую пробу с параметрами HTTP серверной части и проверьте работоспособность серверной части, если она работоспособна.

- После этого шлюз приложений теперь должен перенаправлять одно и то же имя узла "www.contoso.com" в службу приложений, а перенаправление произойдет на том же имени узла. Вы можете проверить приведенные ниже примеры заголовков запросов и ответов.

Чтобы реализовать описанные выше действия с помощью PowerShell для существующей установки, выполните приведенный ниже пример сценария PowerShell. Обратите внимание, что мы не использовали параметры-Пиккхостнаме в конфигурации проверки и HTTP-параметров.

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
  ## <a name="next-steps"></a>Следующие шаги

Если описанные выше шаги не устранят проблему, отправьте [запрос в службу поддержки](https://azure.microsoft.com/support/options/).
