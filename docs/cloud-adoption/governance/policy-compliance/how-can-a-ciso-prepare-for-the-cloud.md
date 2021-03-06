---
title: CAF. Подготовка руководителя по информационной безопасности (CISO)
description: Подготовка руководителя по информационной безопасности к работе с облаком
author: BrianBlanchard
ms.date: 10/03/2018
ms.openlocfilehash: a4535163990797decdacdacdcb6a33f0118366e9
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58238345"
---
# <a name="ciso-cloud-readiness-guide"></a>Руководство по подготовке руководителя по информационной безопасности к работе с облаком

Руководство Майкрософт, такое как Azure Cloud Adoption Framework (CAF), не предназначено определять уникальные ограничения по безопасности для тысяч предприятий, на которые распространяется эта документация. При переходе на облачную среду роль руководителя или службы по информационной безопасности (CISO) не заменяется облачными технологиями. Наоборот, руководитель и отдел по информационной безопасности становятся еще более влиятельными и интегрированными в работу организации. В этом руководстве предполагается, что читатель знаком с процессами CISO и стремится модернизировать эти процессы, чтобы реализовать переход на облачные технологии.

При внедрении облака задействуются службы, которые часто не принимали во внимание в традиционных ИТ-средах. Самостоятельное или автоматическое развертывание часто выполняется разработчиками приложений или другими командами ИТ-специалистов, которые обычно не задействованы при развертывании рабочей среды. В некоторых организациях бизнес-подразделения имеют одинаковые возможности самостоятельного обслуживания. Из-за этого может понадобиться составить новые требования к безопасности, которых не было в локальной среде. Централизованная защита является более сложной задачей, так как обеспечение безопасности часто становится общей ответственностью в культуре бизнеса и ИТ. Эта статья поможет CISO подготовиться к такому подходу и принять участие в инкрементальной системе управления.

## <a name="how-can-the-ciso-prepare-for-the-cloud"></a>Подготовка CISO для работы с облаком

Как и большинство политик в организации, политики управления и безопасности стабильно расширяются. Возникающие инциденты безопасности формируют политику, чтобы информировать пользователей и снизить вероятность повторения инцидентов. Такой подход, хоть и является естественным, вызывает расширение политики и технических зависимостей. Стратегии перехода на облачные технологии создают уникальную возможность модернизации и сброса политик. При подготовке к любой стратегии перехода CISO может создать непосредственную и измеримую ценность, выступая в качестве основного участника при [проверке политики](./what-is-a-cloud-policy-review.md).

При такой проверке роль CISO заключается в создании безопасного баланса между ограничениями существующей политики и соответствия и улучшенным состоянием безопасности поставщиков облачных служб. Измерение этого процесса может иметь различные формы. Зачастую он измеряется количеством политик безопасности, которые можно безопасно перенести в поставщик облачных служб.

**Перенос рисков безопасности**. Так как службы перемещаются в модели размещения инфраструктуры как услуги (IaaS), бизнес предполагает меньшую вероятность риска, связанного с подготовкой оборудования. Риск не устраняется, вместо этого он перемещается в поставщик облачных служб. Необходимо, чтобы подход поставщика облачных служб к подготовке аппаратных устройств предоставлял такой же уровень устранения рисков в безопасном повторяемом процессе. В таком случае риск подготовки аппаратного обеспечения удаляется из корпоративной политики. Теперь можно применить новую политику для проверки этих процессов, но риск выполнения перераспределяется, благодаря чему уменьшается общий риск безопасности.

Так как решения "продвигаются вперед", чтобы внедрить модель "платформа как услуга" (PaaS) или "программное обеспечение как услуга" (SaaS), дополнительные риски можно уменьшить, переместить и заменить. Когда риск безопасно перемещается в поставщик облачных служб, затраты на выполнение, мониторинг и применение политик безопасности или других политик соответствия также можно безопасно уменьшить.

**Установка на повышение профессиональной компетенции**. Изменение может казаться устрашающим как для бизнеса, так и для технических специалистов по внедрению. Когда CISO ведет политику с установкой на повышение профессиональной компетенции в организации, естественные страхи заменяются повышенным интересом к обеспечению безопасности и соблюдению политики. Рассматривая [проверку политики](./what-is-a-cloud-policy-review.md), стратегию перехода или простые проверки реализации с установкой на повышение профессиональной компетенции, команды могут двигаться быстрее, но не за счет страха и контролируемого профиля рисков.

## <a name="resources-for-the-chief-information-security-officer"></a>Ресурсы для руководителя по информационной безопасности

Знания об облачной инфраструктуре очень важны для подхода к [проверке политики](./what-is-a-cloud-policy-review.md) с установкой на повышение профессиональной компетенции. Следующие ресурсы помогут CISO лучше распознавать состояние безопасности платформы Microsoft Azure.

Ресурсы по платформе безопасности:

* [Цикл разработки средств безопасности, внутренний аудит](https://www.microsoft.com/sdl/)
* [Обязательное обучение безопасности, наведение справок](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx)
* [Выполнение тестов на проникновение, обнаружение вторжений и атак DDoS, аудит и ведение журнала](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging)
* [Современный центр обработки данных](https://www.microsoft.com/cloud-platform/global-datacenters), физическая безопасность, [безопасная сеть](/azure/security/security-network-overview)
* [Microsoft Azure Security Response in the Cloud](https://aka.ms/SecurityResponsePaper) (Реагирование на нарушения безопасности в облаке Microsoft Azure (PDF-документ))

Конфиденциальность и управление:

* [Постоянное управление данными](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data)
* [Управление расположением данных](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located)
* [Предоставление доступа к данным на ваших условиях](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)
* [Реагирование на запросы правоохранительных органов](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data)
* [Строгие стандарты конфиденциальности](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards)

Соответствие:

* [Центр управления безопасностью](https://www.microsoft.com/trustcenter/default.aspx)
* [Common Controls Hub](https://www.microsoft.com/trustcenter/Common-Controls-Hub)
* [Контрольный список комплексной экспертизы облачных служб](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist)
* [Соответствие служб, расположений и отрасли](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

Прозрачность:

* [Как корпорация Майкрософт защищает данные клиентов в службах Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx)
* [Как корпорация Майкрософт управляет расположением данных в службах Azure](https://azuredatacentermap.azurewebsites.net/)
* [Кто в корпорации Майкрософт имеет доступ к вашим данным и на каких условиях](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)
* [Как корпорация Майкрософт защищает данные клиентов в службах Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx)
* [Проверка сертификации для служб Azure, центр обеспечения прозрачности](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

## <a name="next-steps"></a>Дополнительная информация

Первый шаг к выполнению действия в любой стратегии управления — это [проверка политики](./what-is-a-cloud-policy-review.md). Для выполнения проверки политики полезно ознакомиться со статьей [CAF: How can corporate IT policy become cloud-ready?](./overview.md) (CAF. Как можно подготовить корпоративную ИТ-политику к использованию в облаке?).

> [!div class="nextstepaction"]
> [Подготовка к проверке политики](./what-is-a-cloud-policy-review.md)