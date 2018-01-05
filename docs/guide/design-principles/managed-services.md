---
title: "Использование управляемых служб"
description: "При возможности выбирайте службы PaaS (платформа как услуга) вместо служб IaaS (инфраструктура как услуга)."
author: MikeWasson
layout: LandingPage
ms.openlocfilehash: 7156c073db3e047fb38e031309ddb637a9e44c02
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="use-managed-services"></a>Использование управляемых служб

## <a name="when-possible-use-platform-as-a-service-paas-rather-than-infrastructure-as-a-service-iaas"></a>При возможности выбирайте службы PaaS (платформа как услуга), а не IaaS (инфраструктура как услуга).

IaaS предоставляет лишь набор компонентов. Вы можете собрать из них что угодно, но придется делать это самостоятельно. Управляемые службы намного удобнее в настройке и администрировании. Вам не придется подготавливать виртуальные машины, настраивать виртуальные сети, управлять исправлениями и обновлениями, а также беспокоиться о множестве других проблем, связанных с работой программного обеспечения на виртуальной машине.

Предположим, что в вашем приложении нужна очередь сообщений. Чтобы настроить собственную службу обмена сообщениями на виртуальной машине,вам потребуется установить, например, RabbitMQ. А ведь служебная шина Azure уже предоставляет надежную службу для обмена сообщениями, и ее гораздо проще настроить. Достаточно создать пространство имен служебной шины (например, прямо в скрипте развертывания) и обратиться к ней через клиентский пакет SDK. 

Конечно, у вашего приложения могут быть особые требования, для которых модель IaaS подходит лучше. Но даже для приложений на основе службы IaaS можно найти возможность естественного применения управляемых служб. Например, служб кэширования, управления очередями и хранилища данных.

| Вместо службы... | рекомендуем использовать... |
|-----------------------|-------------|
| Active Directory | Доменные службы Azure Active Directory |
| Elasticsearch | поиск Azure; |
| Hadoop | HDInsight |
| IIS | Служба приложений |
| MongoDB | База данных Cosmos |
| Redis | кэш Azure Redis |
| SQL Server | База данных SQL Azure |

