---
title: "Масштабируемое веб-приложение"
description: "Повышение масштабируемости в веб-приложении, работающем в Microsoft Azure."
author: MikeWasson
pnp.series.title: Azure App Service
pnp.series.prev: basic-web-app
pnp.series.next: multi-region-web-app
ms.date: 11/23/2016
cardTitle: Improve scalability
ms.openlocfilehash: b875b89b87edd5636d90da8b7f8211f965b39937
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="improve-scalability-in-a-web-application"></a>Повышение масштабируемости в веб-приложении

На схеме этой эталонной архитектуры представлены сведения о проверенных методах улучшения масштабируемости и производительности в службе приложений Azure.

![[0]][0]

*Скачайте [файл Visio][visio-download] этой архитектуры.*

## <a name="architecture"></a>Архитектура  

Эта архитектура создана на основе архитектуры, описанной в статье [Basic web application][basic-web-app] (Базовое веб-приложение). Она содержит следующие компоненты:

* **Группа ресурсов**. [Группа ресурсов][resource-group] — это логический контейнер для ресурсов Azure.
* **[Веб-приложение][app-service-web-app]** и **[приложение API][app-service-api-app]**. Обычно современное приложение может включать в себя и веб-сайт, и один (или несколько) веб-API RESTful. Веб-API может использоваться клиентами браузера через технологию AJAX с помощью собственных клиентских приложений или приложений на стороне сервера. Рекомендации по разработке веб-API см. в статье [API design][api-guidance] (Проектирование API).    
* **Веб-задание.** Используйте [веб-задания Azure][webjobs], чтобы выполнять длительные задачи в фоновом режиме. Веб-задания запускаются по расписанию, непрерывно или в ответ на срабатывание триггера, например на помещение сообщения в очередь. Веб-задание выполняется как фоновый процесс в контексте приложения службы приложений.
* **Очередь.** В архитектуре, показанной здесь, приложение выполняет запрос фоновых заданий путем помещения сообщения в очередь [хранилища очередей Azure][queue-storage]. Сообщение вызывает функцию в веб-задании. В качестве альтернативы можно использовать очереди служебной шины. Сравнение см. в статье [Очереди службы хранилища и очереди служебной шины: сходства и различия][queues-compared].
* **Кэш.** Храните полустатические данные в [кэше Redis для Azure][azure-redis].  
* **CDN.** Используйте [сеть доставки содержимого Azure][azure-cdn] (CDN), чтобы выполнять кэширование общедоступного содержимого с меньшей задержкой и более быстрой доставкой содержимого.
* **Хранилище данных.** Используйте [базу данных SQL Azure][sql-db] для реляционных данных. Для нереляционных данных рассмотрите возможность использования хранилища NoSQL (например, [Cosmos DB][documentdb]).
* **Поиск Azure.** Используйте [Поиск Azure][azure-search], чтобы добавлять функции поиска (такие как, например, поисковые запросы, поиск нечетких соответствий и языковой поиск). Служба "Поиск Azure" обычно используется в сочетании с другим хранилищем данных, особенно если первичное хранилище данных требует строгой согласованности. При использовании этого подхода храните достоверные данные в другом хранилище данных и используйте другой индекс поиска в службе "Поиск Azure". Служба "Поиск Azure" может также использоваться для консолидации одного индекса поиска из нескольких хранилищ данных.  
* **Электронные и текстовые сообщения.** Используйте службы сторонних разработчиков, например SendGrid или Twilio, чтобы отправлять сообщения электронной почты или SMS-сообщения вместо создания этой функции непосредственно в данном приложении.

## <a name="recommendations"></a>Рекомендации

Требования могут отличаться от архитектуры, описанной здесь. В качестве отправной точки используйте рекомендации в этом разделе.

### <a name="app-service-apps"></a>Приложения службы приложений
Рекомендуется создавать веб-приложение и веб-API как отдельные приложения службы приложений. Такой подход позволяет запускать их в отдельных планах службы приложений, поэтому они могут масштабироваться независимо друг от друга. Если изначально этот уровень масштабируемости не требуется, можно развертывать приложения в одном плане и позже переместить их в отдельные планы (при необходимости).

> [!NOTE]
> Для планов "Базовый", "Стандартный" и "Премиум" взимается плата за количество экземпляров виртуальных машин в плане, а не за отдельные приложения. Ознакомьтесь с [ценами на службу приложений][app-service-pricing]
> 
> 

Если вы планируете использовать такие возможности мобильных приложений службы приложений, как *Простые таблицы* или *Простые API*, создайте отдельное приложение службы приложений.  Эти возможности зависят от конкретной исполняющей среды приложения для их включения.

### <a name="webjobs"></a>Веб-задания
Рассмотрите возможность развертывания ресурсоемких веб-заданий в пустое приложение службы приложений в рамках отдельного плана службы приложений. Таким образом веб-задание получает зарезервированные экземпляры. Дополнительные сведения см. в статье [Background jobs][webjobs-guidance] (Фоновые задания).  

### <a name="cache"></a>Кэш
Вы можете повысить производительность и масштабируемость с помощью [кэша Redis для Azure][azure-redis], чтобы выполнять кэширование определенных данных. Рассмотрите возможность использования кэша Redis для:

* полустатических данных транзакции;
* состояния сеанса;
* выходных данных в формате HTML. Это может быть полезно в приложениях, которые отображают сложные выходные данные в формате HTML.

Более подробные рекомендации по разработке стратегии кэширования см. в статье [Caching][caching-guidance] (Кэширование).

### <a name="cdn"></a>CDN
Используйте [сеть доставки содержимого Azure][azure-cdn] для кэширования статического содержимого. Основным преимуществом CDN является возможность сократить задержку для пользователей, так как содержимое кэшируется на пограничном сервере, географически близком к пользователю. CDN позволяет сократить нагрузку на приложение, так как такой трафик не обрабатывается приложением.

Если приложение состоит в основном из статических страниц, рассмотрите возможность использования [сети доставки содержимого (CDN) для кэширования всего приложения][cdn-app-service]. Или же поместите статическое содержимое (например, образы, каскадные таблицы стилей и HTML-файлы) в [службу хранилища Azure и используйте CDN для кэширования этих файлов][cdn-storage-account].

> [!NOTE]
> Azure CDN не удается обработать содержимое, для которого требуется аутентификация.
> 
> 

Более подробные инструкции см. в статье [Content Delivery Network][cdn-guidance] (Сеть доставки содержимого).

### <a name="storage"></a>Хранилище
Современные приложения часто обрабатывают большие объемы данных. Чтобы изменить масштаб для облака, важно выбрать правильный тип хранилища. Вот несколько базовых рекомендаций. 

| Что нужно сохранить | Пример | Рекомендуемое хранилище |
| --- | --- | --- |
| Файлы |Образы, документы, PDF-файлы |Хранилище больших двоичных объектов Azure |
| Пары "ключ — значение" |Данные профиля пользователя, обнаруженные по идентификатору пользователя |табличное хранилище Azure; |
| Короткие сообщения, предназначенные для активирования дальнейшей обработки |Запросы на упорядочивание |Хранилище очередей Azure, очередь или раздел служебной шины |
| Нереляционные данные с гибкой схемой, требующей базового запроса |Каталог продукции |База данных документов (например, Azure Cosmos DB, MongoDB или Apache CouchDB) |
| Реляционные данные, требующие более широкой поддержки запросов, строгой схемы и/или строгой согласованности |Список продуктов |База данных SQL Azure |

## <a name="scalability-considerations"></a>Вопросы масштабируемости

Главным преимуществом службы приложений Azure является возможность масштабирования приложения на основе нагрузки. Ниже приведены некоторые рекомендации, которые следует учитывать при планировании масштабирования приложения.

### <a name="app-service-app"></a>Приложение службы приложений
Если решение содержит несколько приложений служб приложений, рассмотрите возможность их развертывания в разных планах службы приложений. Такой подход позволяет масштабировать их независимо друг от друга, так как они выполняются на отдельных экземплярах. 

Аналогичным образом рассмотрите возможность размещения веб-задания в собственном плане так, чтобы фоновые задания не выполнялись на тех экземплярах, которые обрабатывают запросы HTTP.  

### <a name="sql-database"></a>База данных SQL
Увеличьте уровень масштабируемости базы данных SQL путем *сегментирования* базы данных. Сегментирование означает горизонтальное секционирование базы данных. Сегментирование позволяет развернуть базу данных с помощью [инструментов для работы с эластичными базами данных][sql-elastic]. Потенциальные преимущества сегментирования:

- повышение пропускной способности транзакций;
- быстрое выполнение запросов к подмножеству данных.

### <a name="azure-search"></a>поиск Azure;
Служба "Поиск Azure" устраняет затраты на сложный поиск данных из первичного хранилища данных и может масштабироваться для обработки нагрузки. Дополнительные сведения см. в статье [Масштабирование уровней ресурсов для рабочих нагрузок запросов и индексирования в Поиске Azure][azure-search-scaling].

## <a name="security-considerations"></a>Вопросы безопасности
В этом разделе перечислены рекомендации по безопасности, относящиеся к службам Azure, описанным в этой статье. Это не полный список рекомендаций по обеспечению безопасности. Дополнительные рекомендации см. в статье [Проверка подлинности и авторизация в службе приложений Azure][app-service-security].

### <a name="cross-origin-resource-sharing-cors"></a>Общий доступ к ресурсам независимо от источника (CORS)
При создании веб-сайта и веб-API в качестве отдельных приложений веб-сайт не может выполнить вызовы AJAX со стороны клиента в API, пока не будет включен CORS.

> [!NOTE]
> Параметры безопасности веб-браузера предотвращают отправку запросов AJAX с веб-страницы к другому домену. Такое ограничение называется политикой одного источника. Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта. CORS — это стандарт консорциума W3C, позволяющий серверу смягчить ограничения политики одного источника и разрешающий выполнять некоторые запросы независимо от источника, а другие — отклонять.
> 
> 

В службах приложений реализована встроенная поддержка CORS без необходимости написания кода приложения. Дополнительные сведения см. в статье [Создание API RESTful Node.js и его развертывание в приложении API в Azure][cors]. Добавьте веб-сайт в список разрешенных источников для API-интерфейса.

### <a name="sql-database-encryption"></a>Шифрование базы данных SQL
Используйте [прозрачное шифрование данных][sql-encryption] для шифрования неактивных данных, хранящихся в базе данных. Эта функция выполняет шифрование и расшифровку всей базы данных в режиме реального времени (включая резервные копии и файлы журнала транзакций) и не требует изменений в приложении. Шифрование вызывает небольшую задержку, поэтому рекомендуется разбить данные. Они должны быть безопасными в собственной базе данных, и шифрование необходимо выполнять только для этой базы данных.  
  

<!-- links -->

[api-guidance]: ../../best-practices/api-design.md
[app-service-security]: /azure/app-service-web/web-sites-security
[app-service-web-app]: /azure/app-service-web/app-service-web-overview
[app-service-api-app]: /azure/app-service-api/app-service-api-apps-why-best-platform
[app-service-pricing]: https://azure.microsoft.com/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/services/cdn/
[azure-redis]: https://azure.microsoft.com/services/cache/
[azure-search]: https://azure.microsoft.com/documentation/services/search/
[azure-search-scaling]: /azure/search/search-capacity-planning
[background-jobs]: ../../best-practices/background-jobs.md
[basic-web-app]: basic-web-app.md
[basic-web-app-scalability]: basic-web-app.md#scalability-considerations
[caching-guidance]: ../../best-practices/caching.md
[cdn-app-service]: /azure/app-service-web/cdn-websites-with-cdn
[cdn-storage-account]: /azure/cdn/cdn-create-a-storage-account-with-cdn
[cdn-guidance]: ../../best-practices/cdn.md
[cors]: /azure/app-service-api/app-service-api-cors-consume-javascript
[documentdb]: https://azure.microsoft.com/documentation/services/documentdb/
[queue-storage]: /azure/storage/storage-dotnet-how-to-use-queues
[queues-compared]: /azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted
[resource-group]: /azure/azure-resource-manager/resource-group-overview#resource-groups
[sql-db]: https://azure.microsoft.com/documentation/services/sql-database/
[sql-elastic]: /azure/sql-database/sql-database-elastic-scale-introduction
[sql-encryption]: https://msdn.microsoft.com/library/dn948096.aspx
[tm]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.azureedge.net/cdn/app-service-reference-architectures.vsdx
[web-app-multi-region]: ./multi-region.md
[webjobs-guidance]: ../../best-practices/background-jobs.md
[webjobs]: /azure/app-service/app-service-webjobs-readme
[0]: ./images/scalable-web-app.png "Веб-приложение в Azure с повышенной масштабируемостью"