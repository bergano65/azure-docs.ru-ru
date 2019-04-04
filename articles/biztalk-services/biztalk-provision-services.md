---
title: Создание служб Azure BizTalk на портале Azure | Документы Майкрософт
description: Узнайте, как подготовить или создать службы Azure BizTalk на портале Azure; MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: f5ffd1a9d0e7ff515b0819bb678bf0263f53e0d2
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918779"
---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Создание служб BizTalk с помощью портала Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
> 
> [!TIP]
> Для входа на портал Azure требуются учетная запись и подписка Azure. Если учетной записи нет, можно создать бесплатную пробную учетную запись всего за несколько минут. См. страницу [Бесплатная пробная версия Azure](https://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>Создание службы BizTalk

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

В зависимости от состояния службы BizTalk некоторые операции завершить невозможно. Список этих операций см. в статье [Диаграмма состояния службы BizTalk](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Действия после подготовки
* [Установка сертификата на локальном компьютере](#InstallCert)
* [Добавление сертификата в готовый к выпуску](#AddCert)
* [Получить пространство имен Access Control](#ACS)

#### <a name="InstallCert"></a>Установка сертификата на локальном компьютере

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="AddCert"></a>Добавление готового сертификата для рабочей среды

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="ACS"></a>Извлечение пространства имен контроля доступа

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Это пространство имен Access Control вводится при развертывании проекта службы BizTalk из Visual Studio. Пространство имен Access Control создается автоматически для вашей службы BizTalk.

Значения Access Control можно использовать в любом приложении. При создании служб BizTalk это пространство имен Access Control управляет проверкой подлинности при развертывании службы BizTalk. Для изменения подписки или управления пространством имен выберите **ACTIVE DIRECTORY** в левой области навигации, а затем выберите ваше пространство имен. Параметры перечислены на панели задач.

Если щелкнуть **Управление** , откроется портал управления доступом. На портале управления службы контроля доступа служба BizTalk использует **идентификаторы службы**:  
![Идентификаторы службы ACS доступ управления портала управления][ACSServiceIdentities]

Идентификатор службы Access Control — это набор учетных данных, позволяющих приложениям или клиентам проходить проверку подлинности непосредственно в службе Access Control и получать токен.

> [!IMPORTANT]
> Служба BizTalk использует значение **Владелец** в качестве идентификатора службы по умолчанию и значение **Пароль**. Если вместо значения пароля используется значение симметричного ключа, может возникнуть следующая ошибка:<br/><br/>*Не удалось подключиться к учетной записи службы контроля доступа с помощью указанных учетных данных*
> 
> 

[Управление пространством имен ACS](/previous-versions/azure/azure-services/hh674478(v=azure.100)) приведены некоторые правила и рекомендации.

## <a name="requirements-explained"></a>Пояснение требований
Эти требования не применяются к выпуску Free Edition.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Необходимые элементы</strong></td>
        <td><strong>Их назначение</strong></td>
</tr>
<tr>
<td>Подписка Azure.</td>
<td>Подписка определяет, кто может входить в Azure. Владелец учетной записи создает подписку на странице <a HREF="https://account.windowsazure.com/Subscriptions">подписок Azure</a>.
<br/><br/>
В учетную запись Azure может входить несколько подписок, и ею могут управлять все пользователи, у которых есть соответствующие права. Например, владелец учетной записи Azure создает подписку с именем <em>BizTalkServiceSubscription</em> и предоставляет администраторам BizTalk в компании (например, ContosoBTSAdmins@live.com) доступ к этой подписке. В этом сценарии администраторы BizTalk входят в Azure с полными правами администратора на все размещенные службы в подписке, в том числе службы BizTalk Azure. Администраторы BizTalk не являются владельцами учетной записи Azure и, следовательно, не имеют доступа к каким-либо данным для выставления счетов.
<br/><br/>Дополнительные сведения см. в статье 
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=267577">Использование управления доступом на основе ролей для контроля доступа к ресурсам в подписке Azure</a>.
</td>
</tr>
<tr>
<td>Базы данных SQL Azure</td>
<td>Здесь хранятся таблицы, представления, хранимые процедуры и данные отслеживания, используемые службой BizTalk.
<br/><br/>
При создании службы BizTalk можно использовать существующий сервер SQL Server Azure, базу данных SQL Azure или автоматически создать новый сервер или базу данных.
<br/><br/>
Масштаб базы данных SQL настраивается автоматически. Обычно для службы BizTalk достаточно масштаба по умолчанию. Изменение масштаба может повлиять на цену. См. в разделе <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=234930"> учетные записи и выставление счетов в базе данных Azure SQL</a>
<br/><br/>
<strong>Примечания</strong>
<br/>
<ul>
<li> При создании нового сервера SQL Server и базы данных Azure автоматически включаются службы Azure. Для работы службы BizTalk необходимо, чтобы службы Azure были включены.</li>
<li>При создании новой базы данных SQL Azure на существующем сервере SQL Server Azure правила брандмауэра сервера не изменяются. В результате другие службы Azure могут не получить доступа к базам данных сервера.</li>
</ul>
</td>
</tr>
<tr>
<td>Пространство имен Azure Access Control</td>
<td>Выполняет проверку подлинности с помощью служб BizTalk Azure. Это пространство имен Access Control вводится при развертывании проекта службы BizTalk из Visual Studio. Пространство имен контроля доступа создается автоматически при создании службы BizTalk.</td>
</tr>

<tr>
<td>Учетная запись хранения Azure</td>
<td>Предоставляет доступ к таблицам, BLOB-объектам и очередям, используемым службой BizTalk, для сохранения следующих элементов:

<ul>
<li>Файлы журналов, в которых отслеживается работа службы BizTalk. </li>
<li>При создании соглашения X12 или AS2 между партнерами можно включить функцию архивирования для сохранения свойств сообщений. Эти данные сохраняются в учетной записи хранения.</li>
</ul>
<br/>
При создании службы BizTalk можно использовать существующую или автоматически созданную новую учетную запись хранения.
<br/><br/>
Службе BizTalk достаточно параметров хранилища по умолчанию.
<br/><br/>
При создании учетной записи хранения автоматически создаются первичный ключ и вторичный ключ. Эти ключи позволяют управлять доступом к учетной записи хранения. Служба BizTalk автоматически использует первичный ключ.
<br/><br/>
Дополнительные сведения см. в статье <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285671">Хранилище</a>.
</td>
</tr>

<tr>
<td>Закрытый сертификат SSL</td>
<td>
При создании службы BizTalk Azure также создается URL-адрес HTTPS-узла, включающий имя службы BizTalk. Этот URL-адрес автоматически конфигурируется для использования самозаверяющего сертификата исключительно для разработки. На рабочем этапе вам потребуется личный SSL-сертификат.
<br/><br/>
<strong>Сведения о сертификате важно SSL</strong>

<ul>
<li>Сертификат должен иметь срок действия менее 5 лет.</li>
<li>Все личные сертификаты требуют наличия пароля. Запомните этот пароль, а также сообщите его вашим администраторам.</li>
<li>Самозаверяющие сертификаты используются в средах разработки/тестирования. При использовании самозаверяющих сертификатов импортируйте сертификат в хранилище личных сертификатов и хранилище доверенных корневых центров сертификации пользователей.</li>
</ul>
<br/>При отправке центру сертификации запроса на сертификат для рабочей среды укажите следующие свойства сертификата:
<br/>

<ul>
<li><strong>Расширенное использование ключа</strong>: Как минимум службы BizTalk Azure требуют проверки подлинности сервера.</li>
<li><strong>Общее имя</strong>: Введите полное доменное имя (FQDN) URL-адрес службы BizTalk Azure. См. раздел <a HREF="#CreateService">Создание службы BizTalk</a> в этой статье.</li>
</ul>
<br/>
По окончании создания службы BizTalk можно добавить новый или имеющийся сертификат.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>через гибридные подключения
При создании службы BizTalk Azure становится доступной вкладка **Гибридные подключения** :

![вкладка "Гибридные подключения"][HybridConnectionTab]

Гибридные подключения используются для подключения веб-сайта Azure или мобильной службы Azure к любому локальному ресурсу, использующему статический TCP-порт, например SQL Server, MySQL, веб-интерфейсы API HTTP, мобильные службы и большинство настраиваемых веб-служб.  Гибридные соединения и службы адаптера BizTalk — это не одно и то же. Служба адаптера BizTalk используется для подключения служб BizTalk Azure к локальной бизнес-системе.

 Дополнительные сведения, в том числе о создании гибридных подключений и управлении ими, см. в статье [Гибридные подключения](integration-hybrid-connection-overview.md).

## <a name="next-steps"></a>Дальнейшие действия
После создания службы BizTalk ознакомьтесь со статьей [службы BizTalk: Вкладки панели мониторинга, мониторинг и масштабирование](biztalk-dashboard-monitor-scale-tabs.md). Служба BizTalk готова для использования в приложениях. Чтобы приступить к созданию приложений, перейдите по ссылке [Службы BizTalk Azure](https://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>См. также
* [Службы BizTalk. Таблица выпусков](biztalk-editions-feature-chart.md)<br/>
* [Службы BizTalk. Диаграмма состояния](biztalk-service-state-chart.md)<br/>
* [Службы BizTalk. Архивация и восстановление](biztalk-backup-restore.md)<br/>
* [Службы BizTalk. Регулирование](biztalk-throttling-thresholds.md)<br/>
* [Службы BizTalk. Имя издателя и ключ издателя](biztalk-issuer-name-issuer-key.md)<br/>
* [Инструкции запуска с помощью пакета SDK служб BizTalk Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [через гибридные подключения](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
