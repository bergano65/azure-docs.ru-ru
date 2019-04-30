---
title: Пример сети периметра — построение сети периметра для защиты приложений с брандмауэром и группами безопасности сети | Документация Майкрософт
description: Построение сети периметра с брандмауэром и группы безопасности сети (Nsg)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868371"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>Пример 2: Создание сети периметра для защиты приложений с брандмауэром и группами безопасности сети
[Вернуться к облачным службам Майкрософт и странице безопасности сети][HOME]

В этом примере показано, как создать сеть периметра (также известный как *DMZ*, *демилитаризованная зона*, и *Промежуточная подсеть*) с брандмауэром, четыре компьютера-сервера Windows, и группы безопасности сети (Nsg). Он содержит сведения о каждом из используемые команды для более глубокого понимания каждого этапа. В разделе «Сценарии трафика» предоставляет пошаговое объяснение способа прохождение трафика через уровни защиты в сети периметра. Наконец в разделе «Ссылки» предоставляет полный код и инструкции по созданию этой среды для тестирования и экспериментов с различными сценариями.

![Входящая сеть периметра с виртуальным сетевым Устройством и группы безопасности сети][1]

## <a name="environment"></a>Среда 
Этот пример основан на сценарии с подпиской Azure, которая содержит следующие элементы:

* две облачные службы: FrontEnd001 и BackEnd001.
* Виртуальная сеть CorpNetwork с двумя подсетями: FrontEnd и BackEnd.
* Одну группу безопасности сети, применяемый к обеим подсетям.
* Виртуальное сетевое устройство: Брандмауэр Barracuda NextGen Firewall, подключенных к подсети переднего плана.
* Компьютер с Windows Server, представляет веб-сервер приложения. IIS01.
* Два компьютера Windows Server, которые представляют серверы серверной части приложения: Сервер AppVM01 и AppVM02.
* Компьютер с Windows Server, представляет DNS-сервер. DNS01.

> [!NOTE]
> Несмотря на то, что в этом примере брандмауэр Barracuda NextGen Firewall, может использоваться много виртуальных сетевых устройств.
> 
> 

Сценарий PowerShell в разделе «Ссылки» этой статьи создает большую часть среды, описанные здесь. Виртуальные машины и виртуальные сети создаются с помощью сценария, но эти процессы не описаны в данном документе подробно.

Чтобы создать среду, сделайте следующее.

1. Сохраните XML-файл конфигурации сети, включены в раздел «Ссылки» (обновление с именами, расположения и IP-адреса для соответствия вашему сценарию).
2. Обновление, определяемые пользователем переменные в скрипте PowerShell, в соответствии со средой скрипт будет выполняться (подписки, имена служб и т. д.).
3. Запустите сценарий в PowerShell.

> [!NOTE]
> Регион, указанный в скрипте PowerShell, должен соответствовать региону, в XML-файле конфигурации сети.
>
>

После успешного выполнения скрипта, можно выполнить следующие шаги.

1. Настройка правил брандмауэра. См. в разделе «Правила брандмауэра» этой статьи.
2. Если требуется, можно настроить веб-сервер и сервер приложений с простого веб-приложения, можно протестировать конфигурацию сети периметра. Можно использовать скрипты два приложения, описанные в разделе «Ссылки».

В следующем разделе показано большинство инструкций языка сценариев, связанных с группами безопасности сети.

## <a name="nsgs"></a>Группы безопасности сети
В этом примере создается группа безопасности сети, после чего в нее загружаются шесть правил.

> [!TIP]
> Как правило следует сначала создать конкретные правила «Разрешить» и более общие правила «Deny» Фамилия. Управления назначенным приоритетом, которые представляют собой правила вычисляется первым. После обнаружения трафика, который подходит для конкретного правила оцениваются все остальные правила. Правила группы безопасности сети можно применить в входящего или исходящего направления (относительно подсети).
> 
> 

Декларативно эти правила создаются для входящего трафика:

1. Внутренний трафик DNS (порт 53) разрешается.
2. Трафик RDP (порт 3389) из Интернета к любой виртуальной Машине разрешается.
3. Трафик HTTP (порт 80) из Интернета к виртуальным сетевым Устройством (брандмауэр) разрешается.
4. Весь трафик (все порты) от сервера IIS01 к AppVM01 допускается.
5. Весь трафик (все порты) из Интернета ко всей виртуальной сети (обе подсети) запрещается.
6. Весь трафик (все порты) из подсети FrontEnd к внутренней подсети запрещен.

С учетом этих правил, которые привязаны к каждой подсети, будто входящего трафика из Интернета на веб-сервер HTTP-запроса, оба правила 3 (Разрешить) и правило 5 (запретить) может показаться для применения, однако поскольку правило 3 имеет более высокий приоритет, она будет применяться только. Правило 5 не будет учитывать. Поэтому HTTP-запрос разрешается брандмауэр.

Если тот же трафик пытается достичь сервера DNS01, правило 5 (запретить) для применения, то сначала будет трафик бы обращаться к передавать на сервер. Правило 6 (запретить) блокирует трафик из связи с внутренней подсети (за исключением трафик, разрешенный в правилах 1 и 4) подсети переднего плана. Это обеспечивает защиту внутренней сети, в случае, если злоумышленник скомпрометирует веб-приложения на сервере переднего плана. В этом случае злоумышленник получит ограниченный доступ к «Защищенной» внутренней сети. (Будет злоумышленник может получить доступ к только ресурсам, предоставляемым на сервере AppVM01).

Нет исходящего правила по умолчанию разрешает любой трафик к Интернету. В этом примере мы разрешаем исходящий трафик и не изменяет никаких правил. Чтобы заблокировать трафик в обоих направлениях, требуется определяемая пользователем маршрутизация. Вы узнаете об этом методе в другом примере, в [документе о периметре безопасности основной][HOME].

Описанные здесь правила группы безопасности сети похожи на правила в [пример 1 - построение простой сети Периметра с группами безопасности сети][Example1]. Просмотрите описание группы безопасности сети в этой статье подробно рассматривались каждое правило NSG и его атрибуты.

## <a name="firewall-rules"></a>Правила брандмауэра
Необходимо установить клиент управления на компьютере для управления брандмауэром и создания необходимых конфигураций. См. в разделе документации из брандмауэра (или другого виртуального сетевого устройства) поставщика о том, как управлять мобильными устройствами. В оставшейся части этого раздела описывается конфигурация самого брандмауэра с помощью клиента управления поставщика (не портала Azure или PowerShell).

См. в разделе [администратора Barracuda NG](https://techlib.barracuda.com/NG61/NGAdmin) инструкции по загрузке клиента и подключению к брандмауэру Barracuda, используемый в этом примере.

Необходимо создать правила пересылки в брандмауэре. Так как в данном примере ситуация только направляет Интернет-трафик входящих в брандмауэр и затем на веб-сервер, требуется только одно правило перенаправления NAT. Брандмауэр Barracuda используется в этом примере правило будет правило назначения NAT (Dst NAT) для передачи этого трафика.

Чтобы создать следующее правило (или для проверки существующих правил по умолчанию), выполните следующие действия:
1. На панели мониторинга администратора Barracuda NG клиента, на вкладке «Конфигурация» в **рабочая конфигурация** выберите **Ruleset**. 

   Вызывается сетку **основные правила** показаны существующие активные и отключении брандмауэра.

2. Чтобы создать новое правило, щелкните зеленый небольшой **+** кнопки в правом верхнем углу этой сетки. (Брандмауэр может быть заблокирован. Если вы видите кнопку помечены **блокировки** и не удается создать или изменить правила, нажмите кнопку, чтобы разблокировать набор правил и разрешить редактирование.)
  
3. Чтобы изменить существующее правило, выберите правило, щелкните правой кнопкой мыши и выберите **изменение правила**.

Создать новое правило с именем наподобие **WebTraffic.** 

Значок правила назначения NAT выглядит следующим образом:  ![Значок NAT назначения][2]

Само правило будет выглядеть следующим образом:

![Правила брандмауэра][3]

Любой входящий адрес, который попадает в брандмауэр, пытаясь обратиться к HTTP (порт 80 или 443 для HTTPS) отправляется за пределы интерфейс DHCP1 локальный IP-адрес брандмауэра и перенаправлен на веб-сервер с IP-адресом 10.0.1.5. Так как этот трафик поступает порт 80 и перейдя на веб-сервер на порте 80, изменять порт не требуется. Но в список целевых могло бы быть 10.0.1.5:8080 Если веб-сервер слушал порт 8080, который преобразует входящий порт 80 в брандмауэре для входящего трафика порта 8080 на веб-сервере.

Необходимо также указать способ подключения. Для правила назначения из Интернета динамический SNAT — наиболее подходящий метод.

Несмотря на то, что вы создали только одно правило, очень важно правильно задать его приоритет. В сетке все правила брандмауэра Если это новое правило находится внизу (см. ниже правила BLOCKALL), его будет никогда не учитывать. Убедитесь, что новое правило для веб-трафика находится выше правила BLOCKALL.

После создания правила необходимо передать их в брандмауэр и затем активировать ее. Если вы не выполните эти действия, правило изменения не вступят в силу. В следующем разделе описывается процесс отправки и активации.

## <a name="rule-activation"></a>Активация правила
Теперь, когда правило добавляется в набор правил, необходимо отправить набор правил в брандмауэр и активировать его.

![Активация правила брандмауэра][4]

В правом верхнем углу клиента управления вы увидите группу кнопок. Выберите **отправить изменения** Чтобы отправить измененный набор правил брандмауэра, а затем выберите **активировать**.

Теперь, после активации набора правил брандмауэра, среды завершена. Если требуется, выполнять сценарии примера приложения в разделе «Ссылки», чтобы добавить приложение в среде. При добавлении приложения, можно проверить сценарии трафик, описанные в следующем разделе.

> [!IMPORTANT]
> Следует помнить, что вы не сможете достичь непосредственно веб-сервера. Когда браузер запрашивает страницу HTTP из FrontEnd001.CloudApp.Net, конечная точка HTTP (порт 80) передает трафик в брандмауэр, а не к веб-сервера. Брандмауэр, из-за ранее созданное правило, использует NAT для сопоставления запроса на веб-сервер.
> 
> 

## <a name="traffic-scenarios"></a>Варианты прохождения трафика
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Разрешено) Веб-для веб-серверу через брандмауэр
1. Интернет-пользователь запрашивает страницу HTTP из FrontEnd001.CloudApp.Net (облачная служба Интернета).
2. Облачная служба передает трафик через открытую конечную точку на порте 80 на локальный интерфейс брандмауэра 10.0.1.4:80.
3. Подсеть FrontEnd начинает обработку правила для входящего трафика:
   1. Группы безопасности сети правило 1 (DNS) не применяется. Перемещение к следующему правилу.
   2. Правило группы безопасности сети 2 (RDP) не применяется. Перемещение к следующему правилу.
   3. Группы безопасности сети правило 3 (подключение из Интернета к брандмауэру) применяется. Разрешите трафик. Дальнейшая обработка правил прекращается.
4. Трафик достигает внутреннего IP-адрес брандмауэра (10.0.1.4).
5. Правило переадресации брандмауэром определяет, что это трафика порта 80 и перенаправляет его на веб-серверу IIS01.
6. IIS01 ожидает передачи данных для веб-трафика, получает запрос и начинает обработку запроса.
7. IIS01 запрашивает сведения из экземпляра SQL Server на сервере AppVM01.
8. Нет исходящих правил в подсети FrontEnd, поэтому трафик разрешается.
9. Подсеть BackEnd начинает обработку правила входящего трафика:
   1. Группы безопасности сети правило 1 (DNS) не применяется. Перемещение к следующему правилу.
   2. Правило группы безопасности сети 2 (RDP) не применяется. Перемещение к следующему правилу.
   3. Правило группы безопасности сети 3 (подключение из Интернета к брандмауэру) не применяется. Перемещение к следующему правилу.
   4. Группы безопасности сети правило 4 (IIS01 к AppVM01) применяется. Разрешите трафик. Дальнейшая обработка правил прекращается.
10. Экземпляр SQL Server на сервере AppVM01 получает запрос и отвечает.
11. Так как во внутренней подсети нет исходящих правил, ответ пропускается.
12. Подсеть FrontEnd начинает обработку правила для входящего трафика:
    1. Нет правил группы безопасности сети, к которому применяется входящий трафик из внутренней подсети подсети FrontEnd, поэтому правила группы безопасности сети не применяются.
    2. Системное правило по умолчанию, которое разрешает трафик между подсетями позволяет этот трафик, поэтому трафик разрешается.
13. IIS01 получает ответ от AppVM01, завершает HTTP-ответ и отправляет его запрашивающей стороне.
14. Так как это сеанс NAT от брандмауэра, местом назначения ответа изначально является брандмауэр.
15. Брандмауэр получает ответ от веб-сервера и перенаправляет его обратно пользователю Интернета.
16. Так как в подсети FrontEnd нет исходящих правил, ответ пропускается и Интернет-пользователь получает веб-страницы.

#### <a name="allowed-rdp-to-backend"></a>(Разрешено) Запрос через RDP к серверной части
1. Администратор сервера в Интернете запрашивает сеанс RDP с AppVM01 на BackEnd001.CloudApp.Net:*xxxxx*, где *xxxxx* — порт, назначенный случайным образом для протокола удаленного рабочего СТОЛА на AppVM01. (Можно найти порт, назначенный на портале Azure или с помощью PowerShell).
2. Поскольку брандмауэр прослушивает только адрес FrontEnd001.CloudApp.Net, он не включается в этот поток трафика.
3. Подсеть BackEnd начинает обработку правила входящего трафика:
   1. Группы безопасности сети правило 1 (DNS) не применяется. Перемещение к следующему правилу.
   2. Применить правила группы безопасности сети (RDP). 2. Разрешите трафик. Дальнейшая обработка правил прекращается.
4. Так как нет исходящих правил, правила по умолчанию применяются и возвращаемое трафик.
5. Сеанс RDP включен.
6. AppVM01 запрашивает имя пользователя и пароль.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Разрешено) Web server DNS-запрос серверу DNS
1. Серверу IIS01 web потребностей данных веб-канала по www.data.gov, но должна быть возможность разрешить адрес.
2. Конфигурация сети для виртуальной сети показано DNS01 (10.0.2.4 в подсети BackEnd) как основной DNS-сервер. Сервер IIS01 отправляет DNS-запрос серверу dns01.
3. Так как в подсети FrontEnd нет исходящих правил, трафик разрешается.
4. Подсеть BackEnd начинает обработку правила входящего трафика:
   1. Группы безопасности сети правило 1 (DNS) применяется. Разрешите трафик. Дальнейшая обработка правил прекращается.
5. DNS-сервер получает запрос.
6. DNS-сервер не указан адрес, который кэшируются и запрашивает корневой DNS-сервер в Интернете.
7. Так как во внутренней подсети нет исходящих правил, трафик разрешается.
8. The internet DNS server responds. Так как сеанс инициирован внутри системы, ответ пропускается.
9. DNS-сервер кэширует ответ и отвечает на запрос от сервера IIS01.
10. Так как во внутренней подсети нет исходящих правил, трафик разрешается.
11. Подсеть FrontEnd начинает обработку правила для входящего трафика:
    1. Нет правил группы безопасности сети, к которому применяется входящий трафик из внутренней подсети подсети FrontEnd, поэтому правила группы безопасности сети не применяются.
    2. Системное правило по умолчанию, которое разрешает трафик между подсетями позволяет этот трафик, поэтому трафик разрешается.
12. Сервер IIS01 получает ответ от сервера DNS01.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Разрешено) Доступ к файлам Web server на сервере AppVM01
1. IIS01 запрашивает файл на AppVM01.
2. Так как в подсети FrontEnd нет исходящих правил, трафик разрешается.
3. Подсеть BackEnd начинает обработку правила входящего трафика:
   1. Группы безопасности сети правило 1 (DNS) не применяется. Перемещение к следующему правилу.
   2. Правило группы безопасности сети 2 (RDP) не применяется. Перемещение к следующему правилу.
   3. Правило группы безопасности сети 3 (подключение из Интернета к брандмауэру) не применяется. Перемещение к следующему правилу.
   4. Группы безопасности сети правило 4 (IIS01 к AppVM01) применяется. Разрешите трафик. Дальнейшая обработка правил прекращается.
4. AppVM01 получает запрос и отвечает с файлом (предполагается, что доступ авторизован).
5. Так как во внутренней подсети нет исходящих правил, ответ пропускается.
6. Подсеть FrontEnd начинает обработку правила для входящего трафика:
   1. Нет правил группы безопасности сети, к которому применяется входящий трафик из внутренней подсети подсети FrontEnd, поэтому правила группы безопасности сети не применяются.
   2. Системное правило по умолчанию, которое разрешает трафик между подсетями позволяет этот трафик, поэтому трафик разрешается.
7. Сервер IIS01 получает файл.

#### <a name="denied-web-direct-to-web-server"></a>(Запрещено) Web direct веб-сервер
Так как веб-серверу IIS01 и брандмауэр находятся в той же облачной службе, они совместно используют тот же общедоступный адрес. Поэтому любой HTTP-трафик направляется в брандмауэр. Хотя запрос будет успешно обработан, нельзя перейти непосредственно к веб-сервера. Он передает сведения, как и было задумано, через брандмауэр сначала. См. в первом сценарии, в этом разделе для потока трафика.

#### <a name="denied-web-to-backend-server"></a>(Запрещено) Интернета к тыловому серверу
1. Интернет-пользователь пытается получить доступ к файлу на AppVM01 через службу BackEnd001.CloudApp.Net.
2. Поскольку конечные точки не открыты для общего доступа к файлам, это не будет передан облачной службе и не достигнет сервера.
3. Если для какой-либо причине открыты конечные точки, группы безопасности сети правило 5 (из Интернета к виртуальной сети) блокирует трафик.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Запрещено) Web поиска DNS на DNS-сервере
1. Интернет-пользователь пытается найти внутреннюю запись DNS на DNS01 через службу BackEnd001.CloudApp.Net.
2. Поскольку конечные точки не открыты для DNS, это не будет передан облачной службе и не достигнет сервера.
3. Если для какой-либо причине открыты конечные точки, группы безопасности сети правило 5 (из Интернета к виртуальной сети) блокирует трафик. (Правило 1 [DNS] не относится по двум причинам. Во-первых адрес источника является Интернет, и это правило применяется только в том случае, когда локальной виртуальной сети является источником. Во-вторых это правило является разрешающее правило, поэтому он никогда не запрещает трафик.)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Запрещено) Веб-доступа к SQL через брандмауэр
1. Интернет-пользователь запрашивает данные SQL из FrontEnd001.CloudApp.Net (облачная Интернет-служба).
2. Поскольку конечные точки не открыты для SQL, это не будет передан облачной службе и не достигнет брандмауэра.
3. Если для какой-либо причине открыты конечные точки, подсеть FrontEnd начинает обработку правила входящего трафика:
   1. Группы безопасности сети правило 1 (DNS) не применяется. Перемещение к следующему правилу.
   2. Правило группы безопасности сети 2 (RDP) не применяется. Перемещение к следующему правилу.
   3. Группы безопасности сети правило 3 (подключение из Интернета к брандмауэру) применяется. Разрешите трафик. Дальнейшая обработка правил прекращается.
4. Трафик достигает внутреннего IP-адрес брандмауэра (10.0.1.4).
5. Брандмауэр не имеет правил перенаправления для SQL и отклоняет трафик.

## <a name="conclusion"></a>Заключение
В этом примере показано относительно простой способ для защиты приложения с помощью брандмауэра и изоляции внутренней подсети от входящего трафика.

Можно найти дополнительные примеры и общие сведения о сетевых границ безопасности [здесь][HOME].

## <a name="references"></a>Ссылки
### <a name="full-script-and-network-config"></a>Полный сценарий и Конфигурация сети
Сохраните полный сценарий в файл сценария PowerShell. Сохраните сценарий конфигурации сети в файл с именем NetworkConf2.xml.
При необходимости измените определенные переменные пользователя. Запустите скрипт и следуйте инструкциям в разделе «Правила брандмауэра» этой статьи.

#### <a name="full-script"></a>Полный сценарий
Этот сценарий, в зависимости от определяемых пользователем переменных будет выполните следующие действия:

1. Подключитесь к подписке Azure.
2. Создайте учетную запись хранения.
3. Создайте виртуальную сеть и две подсети, как определено в файле конфигурации сети.
4. Создание четырех виртуальных машин Windows Server.
5. Настройка группы безопасности сети. Конфигурации выполняет следующие действия:
   * Создает группу безопасности сети.
   * Заполняет группу безопасности сети с правилами.
   * Связывает группу безопасности сети к соответствующим подсетям.

Этот сценарий PowerShell следует запускать локально на сервере или компьютере, подключенном к Интернету.

> [!IMPORTANT]
> При выполнении этого скрипта, предупреждения и другие информационные сообщения могут отображаться в PowerShell. Необходимо ли мои опасения сообщения об ошибках, отображаются красным цветом.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>Файл конфигурации сети
Сохраните этот файл XML с обновлены сведения о местоположении, а затем добавить ссылку на этот файл в переменную $NetworkConfigFile в приведенном выше сценарии.

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Примеры сценариев приложения
Если вы хотите установить образец приложения для этого и других примеров сети периметра, см. в разделе [пример сценария приложения][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Входящая сеть периметра с группой безопасности сети"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Значок NAT назначения"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Правило брандмауэра"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Активация правила брандмауэра"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
