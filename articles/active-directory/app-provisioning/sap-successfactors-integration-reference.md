---
title: Справочник по интеграции Azure Active Directory и SAP SuccessFactors
description: Технический подробный обзор подготовки SAP SuccessFactors — управление с управлением персоналом
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 3c1d0d05554fafb4b18d8dc7043cca3e8479b35e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098376"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Как Azure Active Directory подготовка интегрируется с SAP SuccessFactors 

[Azure Active Directory служба подготовки пользователей](../app-provisioning/user-provisioning.md) интегрируется с [центральным персоналом SAP SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) для управления жизненным циклом удостоверений пользователей. Azure Active Directory предлагает три предварительно созданных интеграции: 

* SuccessFactors в локальную Active Directory подготовки пользователей
* SuccessFactors Azure Active Directory подготовки пользователей
* Обратная запись SuccessFactors

В этой статье объясняется, как работает интеграция и как можно настроить поведение подготовки для различных сценариев HR. 

## <a name="establishing-connectivity"></a>Установление подключения 
Подсистема подготовки Azure AD использует обычную проверку подлинности для подключения к конечным точкам API OData центрального сотрудника. При настройке приложения подготовки SuccessFactors используйте параметр *URL-адрес клиента* в разделе *учетные данные администратора* , чтобы настроить [URL-адрес центра обработки данных API](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

Чтобы дополнительно защитить подключение между службой подготовки Azure AD и SuccessFactors, можно добавить диапазоны IP-адресов Azure AD в список разрешений SuccessFactors IP-адресов, выполнив описанные ниже действия.

* Скачайте [последние диапазоны IP-адресов](https://www.microsoft.com/download/details.aspx?id=56519) для общедоступного облака Azure 
* Откройте файл и выполните поиск тегов **AzureActiveDirectory** и **азуреактиведиректоридомаинсервицес** 

  >[!div class="mx-imgBorder"] 
  >![Диапазон IP-адресов Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

* Скопируйте все диапазоны IP-адресов, перечисленные в элементе *аддресспрефиксес* , и используйте диапазон для создания списка ограничений IP-адресов.
* Преобразовывать значения CIDR в диапазоны IP-адресов.  
* Войдите на портал администрирования SuccessFactors, чтобы добавить диапазоны IP-адресов в список разрешений. См. [Примечание о поддержке SAP 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). Теперь в этом средстве можно [вводить диапазоны IP-адресов](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) . 

## <a name="supported-entities"></a>Поддерживаемые сущности
Для каждого пользователя в SuccessFactors служба подготовки Azure AD извлекает следующие сущности. Каждая сущность разворачивается с помощью параметра запроса *$expand* API OData. См. столбец *правила извлечения* ниже. Некоторые сущности развертываются по умолчанию, а некоторые сущности развертываются только в том случае, если в сопоставлении есть определенный атрибут. 

| \# | Сущность SuccessFactors                  | Узел OData     | Правило извлечения |
|----|----------------------------------------|------------------------------|------------------|
| 1  | перперсон                              | *корневой узел*                  | Always (Всегда)           |
| 2  | перперсонал                            | персоналинфонав              | Always (Всегда)           |
| 3  | перфоне                               | фоненав                     | Always (Всегда)           |
| 4  | перемаил                               | емаилнав                     | Always (Всегда)           |
| 5  | емпемплоймент                          | емплойментнав                | Always (Всегда)           |
| 6  | Пользователь                                   | Емплойментнав/Усернав        | Always (Всегда)           |
| 7  | емпжоб                                 | Емплойментнав/Жобинфонав     | Always (Всегда)           |
| 8  | емпемплойменттерминатион               | активимплойментскаунт       | Always (Всегда)           |
| 9  | фокомпани                              | Емплойментнав/Жобинфонав/Компанинав | Только при сопоставлении атрибута Company или companyId |
| 10 | фодепартмент                           | Емплойментнав/Жобинфонав/Департментнав | Только при сопоставлении атрибута Department или departmentId |
| 11 | фобусинессунит                         | Емплойментнав/Жобинфонав/Бусинессунитнав | Только при сопоставлении атрибута businessUnit или Бусинессунитид |
| 12 | фокостцентер                           | Емплойментнав/Жобинфонав/Костцентернав | Только при сопоставлении атрибута costCenter или Костцентерид |
| 13 | фодивисион                             | Емплойментнав/Жобинфонав/Дивисионнав  | Только при сопоставлении атрибута деления или divisionId |
| 14 | фожобкоде                              | Емплойментнав/Жобинфонав/Жобкоденав  | Только при сопоставлении атрибута Жобкоде или Жобкодеид |
| 15 | фопайграде                             | Емплойментнав/Жобинфонав/Пайграденав  | Только при сопоставлении атрибута Пайграде |
| 16 | фолокатион                             | Емплойментнав/Жобинфонав/Локатионнав  | Только при сопоставлении атрибута Location |
| 17 | фокорпоратеаддрессдефлт                | Емплойментнав/Жобинфонав/Аддресснавдефлт  | Если параметр Mapping содержит один из следующих атрибутов: Оффицелокатионаддресс, ОффицелокатионЦити, Оффицелокатионзипкоде |
| 18 | фоевентреасон                          | Емплойментнав/Жобинфонав/Евентреасоннав  | Только при сопоставлении атрибута Евентреасон |
| 19 | емпглобалассигнмент                    | Емплойментнав/Емпглобалассигнментнав | Только при сопоставлении Ассигнменттипе |
| 20 | Поле выбора Емплойменттипе                | Емплойментнав/Жобинфонав/Емплойменттипенав | Только при сопоставлении Емплойменттипе |
| 21 | Поле выбора Емплойикласс                 | Емплойментнав/Жобинфонав/Емплойикласснав | Только при сопоставлении Емплойикласс |
| 22 | Поле выбора Емплстатус                    | Емплойментнав/Жобинфонав/Емплстатуснав | Только при сопоставлении Емплстатус |
| 23 | Поле выбора Ассигнменттипе                | Емплойментнав/Емпглобалассигнментнав/Ассигнменттипенав | Только при сопоставлении Ассигнменттипе |

## <a name="how-full-sync-works"></a>Как работает полная синхронизация
На основе сопоставления атрибутов во время полной синхронизации Служба подготовки Azure AD отправляет следующий запрос API OData "GET" для получения эффективных данных всех активных пользователей. 

> [!div class="mx-tdCol2BreakAll"]
>| Параметр | Описание |
>| ----------|-------------|
>| Узел API OData | Добавляет HTTPS к *URL-адресу клиента*. Пример: `https://api4.successfactors.com` |
>| Конечная точка API OData | `/odata/v2/PerPerson` |
>| Параметр запроса $format OData | `json` |
>| Параметр запроса $filter OData | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| Параметр запроса $expand OData | Значение этого параметра зависит от сопоставленных атрибутов. Пример: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| Параметр запроса OData Кустомпажесизе | `100` |

> [!NOTE]
> Во время первой начальной полной синхронизации Служба подготовки Azure AD не извлекает неактивные или прерванные рабочие данные.

Для каждого пользователя SuccessFactors служба подготовки ищет учетную запись в целевом объекте (Azure AD или в локальной среде Active Directory), используя соответствующий атрибут, определенный в сопоставлении. Например, если *персонидекстернал* сопоставляется с *EmployeeID* и задан как атрибут сопоставления, служба подготовки использует значение *персонидекстернал* для поиска пользователя с фильтром *EmployeeID* . Если найдено совпадение с пользователем, он обновляет целевые атрибуты. Если совпадений не найдено, создается новая запись в целевом объекте. 

Чтобы проверить данные, возвращенные конечной точкой API OData для определенного *персонидекстернал*, обновите *СУКЦЕССФАКТОРСАПИЕНДПОИНТ* в запросе API ниже, указав URL-адрес сервера центра обработки данных API и используйте такой инструмент, как [POST](https://www.postman.com/downloads/) , чтобы вызвать запрос. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Как работает добавочная синхронизация

После полной синхронизации Служба подготовки Azure AD сохраняет *ластексекутионтиместамп* и использует ее для создания разностных запросов для получения добавочных изменений. Атрибуты метки времени, имеющиеся в каждой сущности SuccessFactors, такие как *ластмодифиеддатетиме*, *StartDate*, *EndDate*и *латесттерминатиондате*, оцениваются, чтобы определить, попадает ли изменение между *ластексекутионтиместамп* и *куррентексекутионтиме*. Если да, то изменение записи считается эффективным и обрабатывается для синхронизации. 

## <a name="reading-attribute-data"></a>Чтение данных атрибута

Когда служба подготовки Azure AD запрашивает SuccessFactors, она извлекает результирующий набор JSON. Результирующий набор JSON содержит ряд атрибутов, хранящихся в центральном сотруднике. По умолчанию схема подготовки настроена на получение только подмножества этих атрибутов. 

Чтобы получить дополнительные атрибуты, выполните указанные ниже действия.
    
* Перейдите к **корпоративным приложениям**  ->  **SuccessFactors**  ->  **Подготовка**приложения  ->  **изменение**  ->  **сопоставления атрибутов**подготовки.
* Прокрутите вниз и щелкните " **отобразить дополнительные параметры**".
* Щелкните **изменить список атрибутов для SuccessFactors**. 

> [!NOTE] 
> Если параметр **изменить список атрибутов для SuccessFactors** не отображается в портал Azure, используйте URL-адрес *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* для доступа к странице. 

* В столбце **выражение API** в этом представлении отображаются выражения JSONPath, используемые соединителем.
  >[!div class="mx-imgBorder"] 
  >![Выражение API](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  
* Можно либо изменить существующее значение JSONPath, либо добавить новый атрибут с допустимым выражением JSONPath в схему. 

В следующем разделе приведен список распространенных сценариев для изменения значений JSONPath. 

## <a name="handling-different-hr-scenarios"></a>Обработка различных сценариев HR

JSONPath — это язык запросов для JSON, похожий на XPath for XML. Как и XPath, JSONPath позволяет извлечь и фильтрация данные из полезных данных JSON.
С помощью преобразования JSONPath можно настроить поведение приложения подготовки Azure AD для получения настраиваемых атрибутов и обработки таких сценариев, как перенайм, преобразование рабочих ролей и глобальное назначение. 

В этом разделе описывается, как можно настроить приложение подготовки для следующих сценариев HR: 
* [Получение дополнительных атрибутов](#retrieving-additional-attributes)
* [Получение настраиваемых атрибутов](#retrieving-custom-attributes)
* [Обработка сценария преобразования рабочей роли](#handling-worker-conversion-scenario)
* [Обработка сценария перенайма](#handling-rehire-scenario)
* [Обработка глобального назначения](#handling-global-assignment-scenario)
* [Сценарий обработки параллельных заданий](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Получение дополнительных атрибутов

Схема приложения подготовки Azure AD SuccessFactors по умолчанию поставляется с [предварительно определенными атрибутами 90 и](sap-successfactors-attribute-reference.md)более. Чтобы добавить в схему подготовки новые готовые атрибуты SuccessFactors, выполните описанные ниже действия. 

* Используйте запрос OData ниже, чтобы получить данные для действительного тестового пользователя из центра сотрудников. 

```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

* Определение центральной сущности сотрудника, связанной с атрибутом
  * Если атрибут является частью сущности *емпемплоймент* , найдите атрибут в узле *емплойментнав* . 
  * Если атрибут является частью сущности *пользователя* , найдите атрибут в узле *емплойментнав/усернав* .
  * Если атрибут является частью сущности *емпжоб* , найдите атрибут в узле *емплойментнав/жобинфонав* . 
* Создайте путь JSON, связанный с атрибутом, и добавьте этот новый атрибут в список атрибутов SuccessFactors. 
  * Пример 1. Предположим, что нужно добавить атрибут *окторехире*, который является частью сущности *емплойментнав* , а затем использовать JSONPath`$.employmentNav.results[0].okToRehire`
  * Пример 2. Предположим, что нужно добавить атрибут *TimeZone*, который является частью сущности *усернав* , а затем использовать JSONPath`$.employmentNav.results[0].userNav.timeZone`
  * Пример 3. Предположим, что нужно добавить атрибут *флсастатус*, который является частью сущности *жобинфонав* , а затем использовать JSONPath`$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
* Сохраните схему. 
* Перезапустите подготовку.

### <a name="retrieving-custom-attributes"></a>Получение настраиваемых атрибутов

По умолчанию следующие настраиваемые атрибуты предварительно определены в приложении подготовки Azure AD SuccessFactors: 
* *custom01-custom15* от сущности пользователя (усернав)
* *customString1-customString15* из сущности Емпемплоймент (емплойментнав) с именем *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* из сущности Емпжобинфо (жобинфонав) с именем *empJobNavCustomString1-empNavJobCustomString15*

Предположим, в основном экземпляре сотрудника атрибут *customString35* в *емпжобинфо* хранит описание расположения. Необходимо передать это значение в Active Directory атрибут *фисикалделиверйоффиценаме* . Чтобы настроить сопоставление атрибутов для этого сценария, выполните указанные ниже действия. 

* Измените список атрибутов SuccessFactors, чтобы добавить новый атрибут с именем *empJobNavCustomString35*.
* Задайте выражение API JSONPath для этого атрибута следующим образом:`$.employmentNav.results[0].jobInfoNav.results[0].customString35`
* Сохраните и перезагрузите изменение сопоставления в портал Azure.  
* В колонке сопоставление атрибутов сопоставьте *empJobNavCustomString35* с *фисикалделиверйоффиценаме*.
* Сохраните сопоставление.

Расширение этого сценария: 
* Если требуется сопоставлять атрибут *custom35* из сущности *пользователя* , используйте JSONPath`$.employmentNav.results[0].userNav.custom35`
* Если требуется сопоставлять атрибут *customString35* из сущности *Емпемплоймент* , используйте JSONPath`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Обработка сценария преобразования рабочей роли

Преобразование рабочих ролей — это процесс преобразования существующего сотрудника в полную продолжительность в подрядчик или наоборот. В этом случае сотрудник Central добавляет новую сущность *емпемплоймент* вместе с новой сущностью *пользователя* для той же сущности *Person* . Сущность *пользователя* , вложенная в предыдущую сущность *емпемплоймент* , имеет значение null. Для обработки этого сценария с последующим отображением новых данных о занятости при выполнении преобразования можно выполнить групповое обновление схемы приложения подготовки, выполнив приведенные ниже действия.  

* Откройте колонку сопоставление атрибутов приложения подготовки SuccessFactors. 
* Прокрутите вниз и щелкните " **отобразить дополнительные параметры**".
* Щелкните ссылку **проверить схему** , чтобы открыть редактор схемы. 
  >![Проверка-схема](media/sap-successfactors-integration-reference/review-schema.png#lightbox)
* Щелкните ссылку для **скачивания** , чтобы сохранить копию схемы перед редактированием. 
  >![Download-Schema](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
* В редакторе схемы нажмите клавиши CTRL + H, чтобы открыть элемент управления Find-Replace.
* В текстовом поле найти скопируйте и вставьте значение.`$.employmentNav.results[0]`
* В текстовом поле заменить скопируйте и вставьте значение `$.employmentNav.results[?(@.userNav != null)]` . Обратите внимание на пробелы, окружающие `!=` оператор, что важно для успешной обработки выражения JSONPath. 
  >![Find-Replace-Conversion](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
* Щелкните параметр "заменить все", чтобы обновить схему. 
* Сохраните схему. 
* Описанный выше процесс обновляет все выражения JSONPath следующим образом: 
  * Старые JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Новый JSONPath:`$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
* Перезапустите подготовку. 

### <a name="handling-rehire-scenario"></a>Обработка сценария перенайма

Обычно существует два варианта обработки перенайма: 
* Вариант 1. Создание нового профиля пользователя в центре сотрудников
* Вариант 2. повторное использование существующего профиля пользователя в центре сотрудников

Если в процессе обработки кадров используется вариант 1, то схема подготовки не требует изменений. Если в процессе обработки кадров используется вариант 2, то сотрудники отдела сотрудников добавляют новую сущность *емпемплоймент* вместе с новой сущностью *пользователя* для той же сущности *Person* . В отличие от сценария преобразования, Предыдущая сущность *емпемплоймент* оставляет сущность *пользователя* и не имеет значения NULL. 

Для обработки этого сценария повторного найма (вариант 2), чтобы последние данные о найме выводились для профилей повторного найма, можно выполнить массовое обновление схемы приложения подготовки, выполнив приведенные ниже действия.  

* Откройте колонку сопоставление атрибутов приложения подготовки SuccessFactors. 
* Прокрутите вниз и щелкните " **отобразить дополнительные параметры**".
* Щелкните ссылку **проверить схему** , чтобы открыть редактор схемы.   
* Щелкните ссылку для **скачивания** , чтобы сохранить копию схемы перед редактированием.   
* В редакторе схемы нажмите клавиши CTRL + H, чтобы открыть элемент управления Find-Replace.
* В текстовом поле найти скопируйте и вставьте значение.`$.employmentNav.results[0]`
* В текстовом поле заменить скопируйте и вставьте значение `$.employmentNav.results[-1:]` . Это выражение JSONPath возвращает последнюю запись *емпемплоймент* .   
* Щелкните параметр "заменить все", чтобы обновить схему. 
* Сохраните схему. 
* Описанный выше процесс обновляет все выражения JSONPath следующим образом: 
  * Старые JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Новый JSONPath:`$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
* Перезапустите подготовку. 

### <a name="handling-global-assignment-scenario"></a>Обработка глобального назначения

Когда пользователь в центре сотрудника обрабатывается для глобального назначения, SuccessFactors добавляет новую сущность *емпемплоймент* и устанавливает для *АССИГНМЕНТКЛАСС* значение "GA". Он также создает новую сущность *пользователя* . Таким образом, у пользователя теперь есть:
* Одна *EmpEmployment*  +  сущность*пользователя* емпемплоймент, которая соответствует назначению Home с *ассигнменткласс* , имеет значение "St" и 
* Другая *EmpEmployment*  +  сущность*пользователя* емпемплоймент, которая соответствует глобальному назначению с *ассигнменткласс* , имеет значение "GA"

Чтобы получить атрибуты, относящиеся к стандартному назначению и профилю пользователя глобального назначения, выполните указанные ниже действия. 

* Откройте колонку сопоставление атрибутов приложения подготовки SuccessFactors. 
* Прокрутите вниз и щелкните " **отобразить дополнительные параметры**".
* Щелкните ссылку **проверить схему** , чтобы открыть редактор схемы.   
* Щелкните ссылку для **скачивания** , чтобы сохранить копию схемы перед редактированием.   
* В редакторе схемы нажмите клавиши CTRL + H, чтобы открыть элемент управления Find-Replace.
* В текстовом поле найти скопируйте и вставьте значение.`$.employmentNav.results[0]`
* В текстовом поле заменить скопируйте и вставьте значение `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
* Щелкните параметр "заменить все", чтобы обновить схему. 
* Сохраните схему. 
* Описанный выше процесс обновляет все выражения JSONPath следующим образом: 
  * Старые JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Новый JSONPath:`$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
* Перезагрузите колонку сопоставления атрибутов приложения. 
* Прокрутите вниз и щелкните " **отобразить дополнительные параметры**".
* Щелкните **изменить список атрибутов для SuccessFactors**.
* Добавьте новые атрибуты для выборки данных глобального назначения. Например, если требуется получить имя отдела, связанное с глобальным профилем назначения, можно добавить атрибут **глобалассигнментдепартмент** с выражением JSONPath, для которого задано значение `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
* Теперь можно либо передать значения отдела как Active Directory атрибуты, либо выборочно переносить значения с помощью сопоставления выражений. Пример. в приведенном ниже выражении значение атрибута *отдела* AD устанавливается равным *глобалассигнментдепартмент* , если оно имеется, в противном случае устанавливается значение *Department* , связанное со стандартным присваиванием. 
  * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`
* Сохраните сопоставление. 
* Перезапустите подготовку. 

### <a name="handling-concurrent-jobs-scenario"></a>Сценарий обработки параллельных заданий

Когда у пользователя в центре сотрудника есть одновременные или несколько заданий, существует две *емпемплоймент* и *пользовательские* сущности с *ассигнменткласс* , для которых задано значение "St". Чтобы получить атрибуты, принадлежащие обоим заданиям, выполните указанные ниже действия. 

* Откройте колонку сопоставление атрибутов приложения подготовки SuccessFactors. 
* Прокрутите вниз и щелкните " **отобразить дополнительные параметры**".
* Щелкните **изменить список атрибутов для SuccessFactors**.
* Предположим, вы хотите извлечь отдел, связанный с заданием 1 и заданием 2. Предварительно определенный *Отдел* атрибутов уже выбирает значение Department для первого задания. Можно определить новый атрибут с именем *секонджобдепартмент* и задать для выражения JSONPath значение`$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
* Теперь можно либо передать значения отдела как Active Directory атрибуты, либо выборочно переносить значения с помощью сопоставления выражений. 
* Сохраните сопоставление. 
* Перезапустите подготовку. 

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте, как настроить SuccessFactors для Active Directory подготовки.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Сведения о настройке обратной записи в SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Дополнительные сведения о поддерживаемых атрибутах SuccessFactors для входящей подготовки](sap-successfactors-attribute-reference.md)










