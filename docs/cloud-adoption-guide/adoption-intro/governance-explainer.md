---
title: Пояснения. Принцип работы управления облаком.
description: Здесь поясняется понятие управления ресурсами для Azure и облака
author: petertay
ms.openlocfilehash: 3404beaa719177ee7638feed8a8442b5c3b455c6
ms.sourcegitcommit: 26b04f138a860979aea5d253ba7fecffc654841e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36206486"
---
# <a name="explainer-what-is-cloud-resource-governance"></a><span data-ttu-id="490a1-103">Пояснения. Знакомство с системой управления облачными ресурсами</span><span class="sxs-lookup"><span data-stu-id="490a1-103">Explainer: what is cloud resource governance?</span></span>

<span data-ttu-id="490a1-104">Из статьи [Пояснения. Принцип работы Azure](azure-explainer.md) с пояснениями вы узнали, что Azure представляет собой коллекцию серверов и сетевого оборудования для запуска виртуализированного оборудования и программного обеспечения пользователями.</span><span class="sxs-lookup"><span data-stu-id="490a1-104">In the [how does Azure work](azure-explainer.md) explainer, you learned that Azure is a collection of servers and networking hardware running virtualized hardware and software on behalf of users.</span></span> <span data-ttu-id="490a1-105">Azure дает возможность ИТ-отделу и отделу по разработке вашей организации быть гибкими, упрощая создание, чтение, обновление и удаление ресурсов по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="490a1-105">Azure enables your organization's development and IT departments to be agile by making it easy to create, read, update, and delete resources as needed.</span></span>

<span data-ttu-id="490a1-106">Тем не менее предоставление разработчикам неограниченного доступа к ресурсам может сделать их очень гибкими, но также может привести к незапланированным затратам.</span><span class="sxs-lookup"><span data-stu-id="490a1-106">However, while giving unrestricted resource access to developers can make them very agile, it can also lead to unintended cost consequences.</span></span> <span data-ttu-id="490a1-107">Например, команде разработчиков может быть разрешено развернуть набор ресурсов для тестирования, но после завершения команда может забыть их удалить.</span><span class="sxs-lookup"><span data-stu-id="490a1-107">For example, a development team might be approved to deploy a set of resources for testing but forget to delete them when testing is complete.</span></span> <span data-ttu-id="490a1-108">А организация будет продолжать нести затраты за использование этих ресурсов, даже если это больше не будет утверждено или необходимо.</span><span class="sxs-lookup"><span data-stu-id="490a1-108">These resources will continue to accrue costs even though their use is no longer approved or necessary.</span></span> 

<span data-ttu-id="490a1-109">Решением этой проблемы является **управление** доступом к ресурсам.</span><span class="sxs-lookup"><span data-stu-id="490a1-109">The solution to this problem is resource access **governance**.</span></span> <span data-ttu-id="490a1-110">Управление ссылается на текущий процесс управления, мониторинга и аудита используемых ресурсов Azure для достижения целей и требований организации.</span><span class="sxs-lookup"><span data-stu-id="490a1-110">Governance refers to the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the goals and requirements of your organization.</span></span> 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94] 

<span data-ttu-id="490a1-111">У каждой организации имеются свои цели и требования, что делает универсальный подход практически невозможным.</span><span class="sxs-lookup"><span data-stu-id="490a1-111">These goals and requirements are unique to each organization so it's not possible to have a one-size-fits-all approach to governance.</span></span> <span data-ttu-id="490a1-112">Исходя их этого, в Azure реализовано два основных средства управления: **служба контроля доступа на основе ресурсов (RBAC)** и **политика ресурсов**. Основываясь на этих средствах, каждая организация разрабатывает свою модель управления.</span><span class="sxs-lookup"><span data-stu-id="490a1-112">Rather, Azure implements two primary governance tools, **resource based access control (RBAC)**, and **resource policy**, and it's up to each organization to design their governance model using them.</span></span>

<span data-ttu-id="490a1-113">RBAC определяет роли, а роли определяют возможности пользователя, которому назначена роль.</span><span class="sxs-lookup"><span data-stu-id="490a1-113">RBAC defines roles, and roles define the capabilities for a user that is assigned the role.</span></span> <span data-ttu-id="490a1-114">Например, владелец **роли** включает все возможности (создание, чтение, обновление и удаление) для ресурса, в то время как для роли **читателя** доступна только возможность чтения.</span><span class="sxs-lookup"><span data-stu-id="490a1-114">For example, the **owner** role enables all capabilites (create, read, update, and delete) for a resource, while the  **reader** roles enables only the read capability.</span></span> <span data-ttu-id="490a1-115">Роли могут быть определены с помощью широкой области, которая применяется ко многим типам ресурсов, или с помощью узкой области, которая применима к нескольким из них.</span><span class="sxs-lookup"><span data-stu-id="490a1-115">Roles can be defined with a broad scope that applies to many resources types, or a narrow scope that applies to a few.</span></span> 

<span data-ttu-id="490a1-116">Правила создания ресурсов определяются политиками ресурсов.</span><span class="sxs-lookup"><span data-stu-id="490a1-116">Resource policies define rules for resource creation.</span></span> <span data-ttu-id="490a1-117">Например, политика ресурса может ограничить размер инвентаризационного номера виртуальной машины определенным, предварительно согласованным размером.</span><span class="sxs-lookup"><span data-stu-id="490a1-117">For example, a resource policy can limit the SKU of a VM to a particular pre-appproved size.</span></span> <span data-ttu-id="490a1-118">Или она может применить дополнительный тег с помощью места возникновения затрат, в то время как запрос делается для создания ресурса.</span><span class="sxs-lookup"><span data-stu-id="490a1-118">Or, a resource policy can enforce the addition of a tag with a cost center when the request is made to create the resource.</span></span> 

<span data-ttu-id="490a1-119">При настройке этих средств для организации важным фактором является правильный баланс между управлением и гибкостью.</span><span class="sxs-lookup"><span data-stu-id="490a1-119">When configuring these tools, an important consideration is balancing governance versus organizational agility.</span></span> <span data-ttu-id="490a1-120">Это означает, что чем более ограничительной будет политика в области управления, тем менее гибкими будут разработчики и работники в сфере информационных технологий.</span><span class="sxs-lookup"><span data-stu-id="490a1-120">That is, the more restrictive your governance policy, the less agile your developers and IT workers become.</span></span> <span data-ttu-id="490a1-121">Это происходит потому, что использование ограничительной политики управления может потребовать больше действий, выполняемых вручную, например, чтобы разработчик заполнил форму или отправил электронное письмо человеку из команды управления о создании ресурса вручную.</span><span class="sxs-lookup"><span data-stu-id="490a1-121">This is because a restrictive goverance policy may require more manual steps, such as requiring a developer to fill out a form or send an email to a person on the governance team to manually create a resource.</span></span> <span data-ttu-id="490a1-122">Управляемая команда имеет ограниченные возможности и может отставать. Это приводит к тому, что непродуктивная команда разработчиков ожидает создания ресурсов, а затраты на ненужные ресурсы, в свою очередь, во время ожидания их удаления будут продолжаться.</span><span class="sxs-lookup"><span data-stu-id="490a1-122">The goverance team has finite capabilities and may become backlogged, resulting in unproductive development teams waiting for their resources to be created and unneeded resources accruing costs while they wait to be deleted.</span></span>

## <a name="next-steps"></a><span data-ttu-id="490a1-123">Дополнительная информация</span><span class="sxs-lookup"><span data-stu-id="490a1-123">Next steps</span></span>

<span data-ttu-id="490a1-124">Следующим этапом по внедрению Azure является [изучение цифровых удостоверений в Azure](tenant-explainer.md) и [создание первого пользователя Microsoft Azure Active Directory][docs-add-users-to-aad].</span><span class="sxs-lookup"><span data-stu-id="490a1-124">Your next step in adopting Azure is to [understand digital identity in Azure](tenant-explainer.md) and [create your first user in Azure AD][docs-add-users-to-aad].</span></span>

<!-- Links -->

[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json