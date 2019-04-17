---
title: マイクロサービスでのサービス間通信
description: マイクロサービスでのサービス間通信
author: MikeWasson
ms.date: 10/23/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: 92c25a9624ef697ea6c8ae4a2a053257b1973052
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244573"
---
# <a name="designing-interservice-communication-for-microservices"></a><span data-ttu-id="a7931-103">マイクロサービスのサービス間通信の設計</span><span class="sxs-lookup"><span data-stu-id="a7931-103">Designing interservice communication for microservices</span></span>

<span data-ttu-id="a7931-104">マイクロサービスの間の通信は、効率的かつ堅牢である必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-104">Communication between microservices must be efficient and robust.</span></span> <span data-ttu-id="a7931-105">多数の小さいサービスが相互作用しながら 1 つのトランザクションを実行する場合、これを実現するのは容易ではない場合があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-105">With lots of small services interacting to complete a single transaction, this can be a challenge.</span></span> <span data-ttu-id="a7931-106">この記事では、非同期メッセージングと同期 API の間のトレードオフについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a7931-106">In this article, we look at the tradeoffs between asynchronous messaging versus synchronous APIs.</span></span> <span data-ttu-id="a7931-107">その後、回復性の高いサービス間通信の設計におけるいくつかの課題と、サービス メッシュが実行できる役割について説明します。</span><span class="sxs-lookup"><span data-stu-id="a7931-107">Then we look at some of the challenges in designing resilient interservice communication, and the role that a service mesh can play.</span></span>

## <a name="challenges"></a><span data-ttu-id="a7931-108">課題</span><span class="sxs-lookup"><span data-stu-id="a7931-108">Challenges</span></span>

<span data-ttu-id="a7931-109">ここでは、サービス間通信において発生する主要な課題を示します。</span><span class="sxs-lookup"><span data-stu-id="a7931-109">Here are some of the main challenges arising from service-to-service communication.</span></span> <span data-ttu-id="a7931-110">サービス メッシュ (後で説明します) は、これらの課題の多くに対処するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="a7931-110">Service meshes, described later in this article, are designed to handle many of these challenges.</span></span>

<span data-ttu-id="a7931-111">**回復性**。</span><span class="sxs-lookup"><span data-stu-id="a7931-111">**Resiliency**.</span></span> <span data-ttu-id="a7931-112">特定のマイクロサービスには、数十から場合によっては数百ものインスタンスが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-112">There may be dozens or even hundreds of instances of any given microservice.</span></span> <span data-ttu-id="a7931-113">インスタンスは、さまざまな理由で失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="a7931-113">An instance can fail for any number of reasons.</span></span> <span data-ttu-id="a7931-114">ハードウェア障害や仮想マシンの再起動のように、ノード レベルで障害が発生する場合があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-114">There can be a node-level failure, such as a hardware failure or a VM reboot.</span></span> <span data-ttu-id="a7931-115">インスタンスがクラッシュしたり、要求の負荷が大きすぎて新しい要求を処理できなくなる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="a7931-115">An instance might crash, or be overwhelmed with requests and unable to process any new requests.</span></span> <span data-ttu-id="a7931-116">これらのどのイベントによっても、ネットワーク呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-116">Any of these events can cause a network call to fail.</span></span> <span data-ttu-id="a7931-117">サービス間ネットワーク呼び出しの回復力を高めるのに役立つ 2 つの設計パターンがあります。</span><span class="sxs-lookup"><span data-stu-id="a7931-117">There are two design patterns that can help make service-to-service network calls more resilient:</span></span>

- <span data-ttu-id="a7931-118">**[再試行](../../patterns/retry.md)**。</span><span class="sxs-lookup"><span data-stu-id="a7931-118">**[Retry](../../patterns/retry.md)**.</span></span> <span data-ttu-id="a7931-119">何もしなくても自動的に解消される一時的なエラーのために、ネットワーク呼び出し失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="a7931-119">A network call may fail because of a transient fault that goes away by itself.</span></span> <span data-ttu-id="a7931-120">呼び出し元は、通常、すぐに失敗として処理するのではなく、一定の回数だけ、または構成されたタイムアウト期間が経過するまで、操作を再試行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-120">Rather than fail outright, the caller should typically retry the operation a certain number of times, or until a configured time-out period elapses.</span></span> <span data-ttu-id="a7931-121">ただし、操作がべき等でない場合は、再試行によって意図しない副作用が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-121">However, if an operation is not idempotent, retries can cause unintended side effects.</span></span> <span data-ttu-id="a7931-122">その場合、元の呼び出しが成功したとしても、呼び出し元は応答を取得できません。</span><span class="sxs-lookup"><span data-stu-id="a7931-122">The original call might succeed, but the caller never gets a response.</span></span> <span data-ttu-id="a7931-123">呼び出し元が再試行を行うと、操作が 2 回呼び出される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-123">If the caller retries, the operation may be invoked twice.</span></span> <span data-ttu-id="a7931-124">一般に、POST または PATCH メソッドは、べき等が保証されていないため、再試行することは安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="a7931-124">Generally, it's not safe to retry POST or PATCH methods, because these are not guaranteed to be idempotent.</span></span>

- <span data-ttu-id="a7931-125">**[サーキット ブレーカー](../../patterns/circuit-breaker.md)**。</span><span class="sxs-lookup"><span data-stu-id="a7931-125">**[Circuit Breaker](../../patterns/circuit-breaker.md)**.</span></span> <span data-ttu-id="a7931-126">失敗した要求が多すぎると、保留中の要求がキューに蓄積されるため、ボトルネックの原因になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-126">Too many failed requests can cause a bottleneck, as pending requests accumulate in the queue.</span></span> <span data-ttu-id="a7931-127">これらのブロックされた要求が重要なシステム リソース (メモリ、しきい値、データベース接続など) を保持することで、障害が連鎖する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-127">These blocked requests might hold critical system resources such as memory, threads, database connections, and so on, which can cause cascading failures.</span></span> <span data-ttu-id="a7931-128">サーキット ブレーカー パターンを使うと、失敗する可能性がある操作をサービスで繰り返し再試行することを回避できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-128">The Circuit Breaker pattern can prevent a service from repeatedly trying an operation that is likely to fail.</span></span>

<span data-ttu-id="a7931-129">**負荷分散**。</span><span class="sxs-lookup"><span data-stu-id="a7931-129">**Load balancing**.</span></span> <span data-ttu-id="a7931-130">サービス "A" がサービス "B" を呼び出すとき、要求はサービス "B" の実行中のインスタンスに到達する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-130">When service "A" calls service "B", the request must reach a running instance of service "B".</span></span> <span data-ttu-id="a7931-131">Kubernetes では、`Service` リソースの種類はポッドのグループに対して安定した IP アドレスを提供します。</span><span class="sxs-lookup"><span data-stu-id="a7931-131">In Kubernetes, the `Service` resource type provides a stable IP address for a group of pods.</span></span> <span data-ttu-id="a7931-132">サービスの IP アドレスへのネットワーク トラフィックは、iptable の規則によってポッドに転送されます。</span><span class="sxs-lookup"><span data-stu-id="a7931-132">Network traffic to the service's IP address gets forwarded to a pod by means of iptable rules.</span></span> <span data-ttu-id="a7931-133">既定では、ポッドはランダムに選ばれます。</span><span class="sxs-lookup"><span data-stu-id="a7931-133">By default, a random pod is chosen.</span></span> <span data-ttu-id="a7931-134">サービス メッシュ (後述) は、観察された待機時間や他のメトリックに基づいて、さらにインテリジェントな負荷分散アルゴリズムを提供できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-134">A service mesh (see below) can provide more intelligent load balancing algorithms based on observed latency or other metrics.</span></span>

<span data-ttu-id="a7931-135">**分散トレース**。</span><span class="sxs-lookup"><span data-stu-id="a7931-135">**Distributed tracing**.</span></span> <span data-ttu-id="a7931-136">1 つのトランザクションで複数のサービスが利用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-136">A single transaction may span multiple services.</span></span> <span data-ttu-id="a7931-137">その場合、システムの全体的なパフォーマンスと正常性の監視が困難になります。</span><span class="sxs-lookup"><span data-stu-id="a7931-137">That can make it hard to monitor the overall performance and health of the system.</span></span> <span data-ttu-id="a7931-138">すべてのサービスがログとメトリックを生成する場合でも、それらを結び付ける何らかの手段がないと、役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="a7931-138">Even if every service generates logs and metrics, without some way to tie them together, they are of limited use.</span></span> <span data-ttu-id="a7931-139">分散トレースについては、[ログ記録と監視](../logging-monitoring.md)に関する記事で詳しく説明されていますが、ここでも課題としてそのことに触れます。</span><span class="sxs-lookup"><span data-stu-id="a7931-139">The article [Logging and monitoring](../logging-monitoring.md) talks more about distributed tracing, but we mention it here as a challenge.</span></span>

<span data-ttu-id="a7931-140">**サービスのバージョン管理**。</span><span class="sxs-lookup"><span data-stu-id="a7931-140">**Service versioning**.</span></span> <span data-ttu-id="a7931-141">チームは、新しいバージョンのサービスをデプロイするときに、他のサービスや、そのサービスに依存する外部クライアントで、中断が発生しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-141">When a team deploys a new version of a service, they must avoid breaking any other services or external clients that depend on it.</span></span> <span data-ttu-id="a7931-142">さらに、サービスの複数のバージョンをサイド バイ サイドで実行し、要求を特定のバージョンにルーティングすることが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-142">In addition, you might want to run multiple versions of a service side-by-side, and route requests to a particular version.</span></span> <span data-ttu-id="a7931-143">この問題について詳しくは、「[API のバージョン管理](./api-design.md#api-versioning)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a7931-143">See [API Versioning](./api-design.md#api-versioning) for more discussion of this issue.</span></span>

<span data-ttu-id="a7931-144">**TLS 暗号化と相互 TLS 認証**。</span><span class="sxs-lookup"><span data-stu-id="a7931-144">**TLS encryption and mutual TLS authentication**.</span></span> <span data-ttu-id="a7931-145">セキュリティ上の理由から、サービス間のトラフィックを TLS で暗号化し、相互 TLS 認証を使って呼び出し元を認証することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-145">For security reasons, you may want to encrypt traffic between services with TLS, and use mutual TLS authentication to authenticate callers.</span></span>

## <a name="synchronous-versus-asynchronous-messaging"></a><span data-ttu-id="a7931-146">同期メッセージングと非同期メッセージング</span><span class="sxs-lookup"><span data-stu-id="a7931-146">Synchronous versus asynchronous messaging</span></span>

<span data-ttu-id="a7931-147">マイクロサービス間の通信には、次の 2 つの基本的なメッセージング パターンを使うことができます。</span><span class="sxs-lookup"><span data-stu-id="a7931-147">There are two basic messaging patterns that microservices can use to communicate with other microservices.</span></span>

1. <span data-ttu-id="a7931-148">同期通信。</span><span class="sxs-lookup"><span data-stu-id="a7931-148">Synchronous communication.</span></span> <span data-ttu-id="a7931-149">このパターンでは、サービスは、別のサービスによって公開されている API を、HTTP や gRPC などのプロトコルを使って呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a7931-149">In this pattern, a service calls an API that another service exposes, using a protocol such as HTTP or gRPC.</span></span> <span data-ttu-id="a7931-150">このオプションは、呼び出し元が受信側からの応答を待機するため、同期メッセージング パターンです。</span><span class="sxs-lookup"><span data-stu-id="a7931-150">This option is a synchronous messaging pattern because the caller waits for a response from the receiver.</span></span>

2. <span data-ttu-id="a7931-151">非同期のメッセージ受け渡し。</span><span class="sxs-lookup"><span data-stu-id="a7931-151">Asynchronous message passing.</span></span> <span data-ttu-id="a7931-152">このパターンでは、サービスは応答を待たずにメッセージを送信し、1 つまたは複数のサービスがメッセージを非同期的に処理します。</span><span class="sxs-lookup"><span data-stu-id="a7931-152">In this pattern, a service sends message without waiting for a response, and one or more services process the message asynchronously.</span></span>

<span data-ttu-id="a7931-153">非同期 I/O と非同期プロトコルを区別することが重要です。</span><span class="sxs-lookup"><span data-stu-id="a7931-153">It's important to distinguish between asynchronous I/O and an asynchronous protocol.</span></span> <span data-ttu-id="a7931-154">非同期 I/O とは、I/O が実行されている間、呼び出し元のスレッドがブロックされないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="a7931-154">Asynchronous I/O means the calling thread is not blocked while the I/O completes.</span></span> <span data-ttu-id="a7931-155">非同期 I/O はパフォーマンスにとって重要ですが、実装の詳細はアーキテクチャに関係します。</span><span class="sxs-lookup"><span data-stu-id="a7931-155">That's important for performance, but is an implementation detail in terms of the architecture.</span></span> <span data-ttu-id="a7931-156">非同期プロトコルとは、送信側が応答を待機しないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="a7931-156">An asynchronous protocol means the sender doesn't wait for a response.</span></span> <span data-ttu-id="a7931-157">HTTP クライアントは要求を送信するときに非同期 I/O を使うことができたとしても、HTTP は同期プロトコルです。</span><span class="sxs-lookup"><span data-stu-id="a7931-157">HTTP is a synchronous protocol, even though an HTTP client may use asynchronous I/O when it sends a request.</span></span>

<span data-ttu-id="a7931-158">各パターンにはトレードオフがあります。</span><span class="sxs-lookup"><span data-stu-id="a7931-158">There are tradeoffs to each pattern.</span></span> <span data-ttu-id="a7931-159">要求/応答はよく知られたパラダイムであるため、API の設計の方が、メッセージング システムの設計より自然に感じられるかもしれません。</span><span class="sxs-lookup"><span data-stu-id="a7931-159">Request/response is a well-understood paradigm, so designing an API may feel more natural than designing a messaging system.</span></span> <span data-ttu-id="a7931-160">しかし、非同期メッセージングには、マイクロサービス アーキテクチャで非常に役に立つ利点がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="a7931-160">However, asynchronous messaging has some advantages that can be very useful in a microservices architecture:</span></span>

- <span data-ttu-id="a7931-161">**低い結合性**。</span><span class="sxs-lookup"><span data-stu-id="a7931-161">**Reduced coupling**.</span></span> <span data-ttu-id="a7931-162">メッセージの送信側は、コンシューマーについて知っている必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a7931-162">The message sender does not need to know about the consumer.</span></span>

- <span data-ttu-id="a7931-163">**複数のサブスクライバー**。</span><span class="sxs-lookup"><span data-stu-id="a7931-163">**Multiple subscribers**.</span></span> <span data-ttu-id="a7931-164">パブリッシュ/サブスクライブ モデルを使うと、複数のコンシューマーがイベントの受信をサブスクライブできます。</span><span class="sxs-lookup"><span data-stu-id="a7931-164">Using a pub/sub model, multiple consumers can subscribe to receive events.</span></span> <span data-ttu-id="a7931-165">「[イベント ドリブン アーキテクチャのスタイル](../../guide/architecture-styles/event-driven.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a7931-165">See [Event-driven architecture style](../../guide/architecture-styles/event-driven.md).</span></span>

- <span data-ttu-id="a7931-166">**障害の分離**。</span><span class="sxs-lookup"><span data-stu-id="a7931-166">**Failure isolation**.</span></span> <span data-ttu-id="a7931-167">コンシューマーで障害が発生した場合でも、送信側はメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-167">If the consumer fails, the sender can still send messages.</span></span> <span data-ttu-id="a7931-168">メッセージは、コンシューマーが復旧すると取得されます。</span><span class="sxs-lookup"><span data-stu-id="a7931-168">The messages will be picked up when the consumer recovers.</span></span> <span data-ttu-id="a7931-169">各サービスが独自のライフサイクルを持つマイクロサービス アーキテクチャでは、この機能が特に役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="a7931-169">This ability is especially useful in a microservices architecture, because each service has its own lifecycle.</span></span> <span data-ttu-id="a7931-170">サービスがいつ使用不能になっても、またはサービスをいつ新しいバージョンに置き換えても、問題はありません。</span><span class="sxs-lookup"><span data-stu-id="a7931-170">A service could become unavailable or be replaced with a newer version at any given time.</span></span> <span data-ttu-id="a7931-171">非同期メッセージングは、断続的なダウンタイムを処理できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-171">Asynchronous messaging can handle intermittent downtime.</span></span> <span data-ttu-id="a7931-172">一方、同期 API では、ダウンストリーム サービスが使用可能である必要があり、使用できないと操作は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a7931-172">Synchronous APIs, on the other hand, require the downstream service to be available or the operation fails.</span></span>

- <span data-ttu-id="a7931-173">**応答性**。</span><span class="sxs-lookup"><span data-stu-id="a7931-173">**Responsiveness**.</span></span> <span data-ttu-id="a7931-174">アップストリームのサービスは、ダウンストリームのサービスを待つ必要がなければ、より速く応答できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-174">An upstream service can reply faster if it does not wait on downstream services.</span></span> <span data-ttu-id="a7931-175">これは、マイクロサービス アーキテクチャで特に役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="a7931-175">This is especially useful in a microservices architecture.</span></span> <span data-ttu-id="a7931-176">サービスの依存関係が連鎖している場合 (たとえば、サービス A が B を呼び出し、B が C を呼び出している場合)、同期呼び出しでの待機により待機時間が許容できないほど長くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-176">If there is a chain of service dependencies (service A calls B, which calls C, and so on), waiting on synchronous calls can add unacceptable amounts of latency.</span></span>

- <span data-ttu-id="a7931-177">**負荷平準化**。</span><span class="sxs-lookup"><span data-stu-id="a7931-177">**Load leveling**.</span></span> <span data-ttu-id="a7931-178">キューは、受信側が独自の速度でメッセージを処理できるように、ワークロードを平準化するためのバッファーとして機能できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-178">A queue can act as a buffer to level the workload, so that receivers can process messages at their own rate.</span></span>

- <span data-ttu-id="a7931-179">**ワークフロー**。</span><span class="sxs-lookup"><span data-stu-id="a7931-179">**Workflows**.</span></span> <span data-ttu-id="a7931-180">キューを使うと、ワークフローの各ステップの後にメッセージのチェックポイントを設けることで、ワークフローを管理できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-180">Queues can be used to manage a workflow, by check-pointing the message after each step in the workflow.</span></span>

<span data-ttu-id="a7931-181">ただし、非同期メッセージングを効果的に使うためには、いくつかの課題もあります。</span><span class="sxs-lookup"><span data-stu-id="a7931-181">However, there are also some challenges to using asynchronous messaging effectively.</span></span>

- <span data-ttu-id="a7931-182">**メッセージング インフラストラクチャとの結合**。</span><span class="sxs-lookup"><span data-stu-id="a7931-182">**Coupling with the messaging infrastructure**.</span></span> <span data-ttu-id="a7931-183">特定のメッセージング インフラストラクチャを使うと、そのインフラストラクチャとの間に密接な結合が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-183">Using a particular messaging infrastructure may cause tight coupling with that infrastructure.</span></span> <span data-ttu-id="a7931-184">その場合、後で別のメッセージング インフラストラクチャに切り替えるのが困難になります。</span><span class="sxs-lookup"><span data-stu-id="a7931-184">It will be difficult to switch to another messaging infrastructure later.</span></span>

- <span data-ttu-id="a7931-185">**待機時間**。</span><span class="sxs-lookup"><span data-stu-id="a7931-185">**Latency**.</span></span> <span data-ttu-id="a7931-186">メッセージ キューがいっぱいになった場合、操作のエンド ツー エンドの待機時間が長くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-186">End-to-end latency for an operation may become high if the message queues fill up.</span></span>

- <span data-ttu-id="a7931-187">**コスト**。</span><span class="sxs-lookup"><span data-stu-id="a7931-187">**Cost**.</span></span> <span data-ttu-id="a7931-188">スループットを高くすると、メッセージング インフラストラクチャの金銭的コストが大きくなることがあります。</span><span class="sxs-lookup"><span data-stu-id="a7931-188">At high throughputs, the monetary cost of the messaging infrastructure could be significant.</span></span>

- <span data-ttu-id="a7931-189">**複雑さ**。</span><span class="sxs-lookup"><span data-stu-id="a7931-189">**Complexity**.</span></span> <span data-ttu-id="a7931-190">非同期メッセージングの処理は、簡単なタスクではありません。</span><span class="sxs-lookup"><span data-stu-id="a7931-190">Handling asynchronous messaging is not a trivial task.</span></span> <span data-ttu-id="a7931-191">たとえば、メッセージの重複は、重複除去を行うか、または操作をべき等にすることによって、処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-191">For example, you must handle duplicated messages, either by de-duplicating or by making operations idempotent.</span></span> <span data-ttu-id="a7931-192">また、非同期メッセージングを使って要求/応答のセマンティクスを実装するのは困難です。</span><span class="sxs-lookup"><span data-stu-id="a7931-192">It's also hard to implement request-response semantics using asynchronous messaging.</span></span> <span data-ttu-id="a7931-193">応答を送信するには、別のキューに加えて、要求メッセージと応答メッセージを関連付ける手段が必要です。</span><span class="sxs-lookup"><span data-stu-id="a7931-193">To send a response, you need another queue, plus a way to correlate request and response messages.</span></span>

- <span data-ttu-id="a7931-194">**スループット**。</span><span class="sxs-lookup"><span data-stu-id="a7931-194">**Throughput**.</span></span> <span data-ttu-id="a7931-195">メッセージで "*キュー セマンティクス*" が必要な場合は、キューがシステムのボトルネックになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-195">If messages require *queue semantics*, the queue can become a bottleneck in the system.</span></span> <span data-ttu-id="a7931-196">メッセージごとに、少なくとも 1 つのキュー操作と 1 つのデキュー操作が必要です。</span><span class="sxs-lookup"><span data-stu-id="a7931-196">Each message requires at least one queue operation and one dequeue operation.</span></span> <span data-ttu-id="a7931-197">さらに、キュー セマンティクスでは、一般に、メッセージング インフラストラクチャ内に何らかの種類のロックが必要になります。</span><span class="sxs-lookup"><span data-stu-id="a7931-197">Moreover, queue semantics generally require some kind of locking inside the messaging infrastructure.</span></span> <span data-ttu-id="a7931-198">キューが管理されたサービスの場合は、キューがクラスターの仮想ネットワークの外部にあるため、待機時間が増える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-198">If the queue is a managed service, there may be additional latency, because the queue is external to the cluster's virtual network.</span></span> <span data-ttu-id="a7931-199">これらの問題はメッセージをバッチ処理することで軽減されますが、コードは複雑になります。</span><span class="sxs-lookup"><span data-stu-id="a7931-199">You can mitigate these issues by batching messages, but that complicates the code.</span></span> <span data-ttu-id="a7931-200">メッセージにキュー セマンティクスが必要ない場合は、キューの代わりにイベント "*ストリーム*" を使うことができる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-200">If the messages don't require queue semantics, you might be able to use an event *stream* instead of a queue.</span></span> <span data-ttu-id="a7931-201">詳しくは、「[イベント ドリブン アーキテクチャのスタイル](../../guide/architecture-styles/event-driven.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a7931-201">For more information, see [Event-driven architectural style](../../guide/architecture-styles/event-driven.md).</span></span>

## <a name="drone-delivery-choosing-the-messaging-patterns"></a><span data-ttu-id="a7931-202">ドローン配送:メッセージング パターンの選択</span><span class="sxs-lookup"><span data-stu-id="a7931-202">Drone Delivery: Choosing the messaging patterns</span></span>

<span data-ttu-id="a7931-203">以上の考慮事項を念頭に置き、開発チームではドローン配送アプリケーションの設計に関して次のような選択が行われました。</span><span class="sxs-lookup"><span data-stu-id="a7931-203">With these considerations in mind, the development team made the following design choices for the Drone Delivery application</span></span>

- <span data-ttu-id="a7931-204">インジェスト サービスでは、クライアント アプリケーションが配送のスケジュール、更新、キャンセルに使うことができるパブリック REST API を公開します。</span><span class="sxs-lookup"><span data-stu-id="a7931-204">The Ingestion service exposes a public REST API that client applications use to schedule, update, or cancel deliveries.</span></span>

- <span data-ttu-id="a7931-205">インジェスト サービスは、Event Hubs を使って、Scheduler サービスに非同期メッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="a7931-205">The Ingestion service uses Event Hubs to send asynchronous messages to the Scheduler service.</span></span> <span data-ttu-id="a7931-206">インジェストに必要な負荷平準化を実装するには、非同期メッセージが必要です。</span><span class="sxs-lookup"><span data-stu-id="a7931-206">Asynchronous messages are necessary to implement the load-leveling that is required for ingestion.</span></span> 

- <span data-ttu-id="a7931-207">アカウント、配送、パッケージ、ドローン、サードパーティ トランスポートの各サービスはすべて、内部 REST API を公開します。</span><span class="sxs-lookup"><span data-stu-id="a7931-207">The Account, Delivery, Package, Drone, and Third-party Transport services all expose internal REST APIs.</span></span> <span data-ttu-id="a7931-208">Scheduler サービスは、これらの API を呼び出してユーザーの要求を実行します。</span><span class="sxs-lookup"><span data-stu-id="a7931-208">The Scheduler service calls these APIs to carry out a user request.</span></span> <span data-ttu-id="a7931-209">同期 API を使う理由の 1 つは、Scheduler は各ダウンストリーム サービスから応答を取得する必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="a7931-209">One reason to use synchronous APIs is that the Scheduler needs to get a response from each of the downstream services.</span></span> <span data-ttu-id="a7931-210">いずれかのダウンストリーム サービスでの障害は、操作全体が失敗することを意味します。</span><span class="sxs-lookup"><span data-stu-id="a7931-210">A failure in any of the downstream services means the entire operation failed.</span></span> <span data-ttu-id="a7931-211">ただし、バックエンド サービスを呼び出すことによってもたらされる待機時間の大きさという潜在的な問題があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-211">However, a potential issue is the amount of latency that is introduced by calling the backend services.</span></span>

- <span data-ttu-id="a7931-212">いずれかのダウンストリーム サービスで一時的ではないエラーが発生した場合は、トランザクション全体を失敗としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-212">If any downstream service has a non-transient failure, the entire transaction should be marked as failed.</span></span> <span data-ttu-id="a7931-213">このような場合に対処するため、Scheduler サービスはスーパーバイザーに非同期メッセージを送信し、スーパーバイザーが補正トランザクションをスケジュールできるようにします。</span><span class="sxs-lookup"><span data-stu-id="a7931-213">To handle this case, the Scheduler service sends an asynchronous message to the Supervisor, so that the Supervisor can schedule compensating transactions.</span></span>

- <span data-ttu-id="a7931-214">配送サービスによって公開されるパブリック API を使うことで、クライアントは配送の状態を取得できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-214">The Delivery service exposes a public API that clients can use to get the status of a delivery.</span></span> <span data-ttu-id="a7931-215">[API ゲートウェイ](./gateway.md)に関する記事では、どの API がどのサービスによって公開されているのかをクライアントが知らなくてもいいように、API ゲートウェイが基になるサービスをクライアントから隠ぺいする方法が説明されています。</span><span class="sxs-lookup"><span data-stu-id="a7931-215">In the article [API gateway](./gateway.md), we discuss how an API gateway can hide the underlying services from the client, so the client doesn't need to know which services expose which APIs.</span></span>

- <span data-ttu-id="a7931-216">ドローンの飛行中、ドローン サービスはドローンの現在の場所と状態を含むイベントを送信します。</span><span class="sxs-lookup"><span data-stu-id="a7931-216">While a drone is in flight, the Drone service sends events that contain the drone's current location and status.</span></span> <span data-ttu-id="a7931-217">配送サービスはこれらのイベントをリッスンして、配送の状態を追跡します。</span><span class="sxs-lookup"><span data-stu-id="a7931-217">The Delivery service listens to these events in order to track the status of a delivery.</span></span>

- <span data-ttu-id="a7931-218">配送の状態が変化すると、配送サービスは `DeliveryCreated` や `DeliveryCompleted` などの配送状態イベントを送信します。</span><span class="sxs-lookup"><span data-stu-id="a7931-218">When the status of a delivery changes, the Delivery service sends a delivery status event, such as `DeliveryCreated` or `DeliveryCompleted`.</span></span> <span data-ttu-id="a7931-219">どのサービスでもこれらのイベントをサブスクライブできます。</span><span class="sxs-lookup"><span data-stu-id="a7931-219">Any service can subscribe to these events.</span></span> <span data-ttu-id="a7931-220">現在の設計では配送サービスが唯一のサブスクライバーですが、後で他のサブスクライバーが追加される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-220">In the current design, the Delivery service is the only subscriber, but there might be other subscribers later.</span></span> <span data-ttu-id="a7931-221">たとえば、イベントがリアルタイム分析サービスに送られるかもしれません。</span><span class="sxs-lookup"><span data-stu-id="a7931-221">For example, the events might go to a real-time analytics service.</span></span> <span data-ttu-id="a7931-222">また、Scheduler は応答を待つ必要がないため、サブスクライバーが追加されてもワークフローのメインのパスに影響はありません。</span><span class="sxs-lookup"><span data-stu-id="a7931-222">And because the Scheduler doesn't have to wait for a response, adding more subscribers doesn't affect the main workflow path.</span></span>

![ドローン通信の図](../images/drone-communication.png)

<span data-ttu-id="a7931-224">配送状態イベントはドローンの場所のイベントから派生することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a7931-224">Notice that delivery status events are derived from drone location events.</span></span> <span data-ttu-id="a7931-225">たとえば、ドローンが配送先に着いてパッケージをドロップすると、配送サービスはこれを DeliveryCompleted イベントに変換します。</span><span class="sxs-lookup"><span data-stu-id="a7931-225">For example, when a drone reaches a delivery location and drops off a package, the Delivery service translates this into a DeliveryCompleted event.</span></span> <span data-ttu-id="a7931-226">これは、ドメイン モデルの観点から考える場合の例です。</span><span class="sxs-lookup"><span data-stu-id="a7931-226">This is an example of thinking in terms of domain models.</span></span> <span data-ttu-id="a7931-227">前に説明したように、ドローン管理は境界が異なるコンテキストに属しています。</span><span class="sxs-lookup"><span data-stu-id="a7931-227">As described earlier, Drone Management belongs in a separate bounded context.</span></span> <span data-ttu-id="a7931-228">ドローン イベントは、ドローンの物理的な場所を伝達します。</span><span class="sxs-lookup"><span data-stu-id="a7931-228">The drone events convey the physical location of a drone.</span></span> <span data-ttu-id="a7931-229">一方、配送イベントは配送状態の変化を表し、これは異なるビジネス エンティティです。</span><span class="sxs-lookup"><span data-stu-id="a7931-229">The delivery events, on the other hand, represent changes in the status of a delivery, which is a different business entity.</span></span>

## <a name="using-a-service-mesh"></a><span data-ttu-id="a7931-230">サービス メッシュの使用</span><span class="sxs-lookup"><span data-stu-id="a7931-230">Using a service mesh</span></span>

<span data-ttu-id="a7931-231">"*サービス メッシュ*" は、サービス間通信を処理するソフトウェア レイヤーです。</span><span class="sxs-lookup"><span data-stu-id="a7931-231">A *service mesh* is a software layer that handles service-to-service communication.</span></span> <span data-ttu-id="a7931-232">サービス メッシュは、前のセクションで挙げた問題の多くに対処し、これらの問題に対する責任をマイクロサービス自体から共有レイヤーに移すように設計されています。</span><span class="sxs-lookup"><span data-stu-id="a7931-232">Service meshes are designed to address many of the concerns listed in the previous section, and to move responsibility for these concerns away from the microservices themselves and into a shared layer.</span></span> <span data-ttu-id="a7931-233">サービス メッシュは、クラスター内のマイクロサービス間のネットワーク通信をインターセプトするプロキシとして機能します。</span><span class="sxs-lookup"><span data-stu-id="a7931-233">The service mesh acts as a proxy that intercepts network communication between microservices in the cluster.</span></span>

> [!NOTE]
> <span data-ttu-id="a7931-234">サービス メッシュは[アンバサダー パターン](../../patterns/ambassador.md)の例であり、アプリケーションに代わってネットワーク要求を送信するヘルパー サービスです。</span><span class="sxs-lookup"><span data-stu-id="a7931-234">Service mesh is an example of the [Ambassador pattern](../../patterns/ambassador.md) &mdash; a helper service that sends network requests on behalf of the application.</span></span>

<span data-ttu-id="a7931-235">現在、Kubernetes でのサービス メッシュの主なオプションは、[linkerd](https://linkerd.io/) と [Istio](https://istio.io/) です。</span><span class="sxs-lookup"><span data-stu-id="a7931-235">Right now, the main options for a service mesh in Kubernetes are [linkerd](https://linkerd.io/) and [Istio](https://istio.io/).</span></span> <span data-ttu-id="a7931-236">どちらのテクノロジも急速に発展しています。</span><span class="sxs-lookup"><span data-stu-id="a7931-236">Both of these technologies are evolving rapidly.</span></span> <span data-ttu-id="a7931-237">ただし、linkerd と Istio のどちらにも次のような機能が共通して含まれます。</span><span class="sxs-lookup"><span data-stu-id="a7931-237">However, some features that both linkerd and Istio have in common include:</span></span>

- <span data-ttu-id="a7931-238">検出された待機時間または未処理の要求の数に基づく、セッション レベルでの負荷分散。</span><span class="sxs-lookup"><span data-stu-id="a7931-238">Load balancing at the session level, based on observed latencies or number of outstanding requests.</span></span> <span data-ttu-id="a7931-239">これにより、Kubernetes によって提供されるレイヤー 4 の負荷分散のパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="a7931-239">This can improve performance over the layer-4 load balancing that is provided by Kubernetes.</span></span>

- <span data-ttu-id="a7931-240">URL パス、ホスト ヘッダー、API のバージョン、または他のアプリケーション レベルのルールに基づく、レイヤー 7 のルーティング。</span><span class="sxs-lookup"><span data-stu-id="a7931-240">Layer-7 routing based on URL path, Host header, API version, or other application-level rules.</span></span>

- <span data-ttu-id="a7931-241">失敗した要求の再試行。</span><span class="sxs-lookup"><span data-stu-id="a7931-241">Retry of failed requests.</span></span> <span data-ttu-id="a7931-242">サービス メッシュは HTTP のエラー コードを認識し、失敗した要求を自動的に再試行できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-242">A service mesh understands HTTP error codes, and can automatically retry failed requests.</span></span> <span data-ttu-id="a7931-243">ユーザーは、再試行の最大回数とタイムアウト時間を構成して、最大待機時間を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="a7931-243">You can configure that maximum number of retries, along with a timeout period in order to bound the maximum latency.</span></span>

- <span data-ttu-id="a7931-244">サーキット ブレーク。</span><span class="sxs-lookup"><span data-stu-id="a7931-244">Circuit breaking.</span></span> <span data-ttu-id="a7931-245">インスタンスで要求が常に失敗する場合、サービス メッシュはそのインスタンスを一時的に利用不可としてマークします。</span><span class="sxs-lookup"><span data-stu-id="a7931-245">If an instance consistently fails requests, the service mesh will temporarily mark it as unavailable.</span></span> <span data-ttu-id="a7931-246">バックオフ期間の後、サービス メッシュはそのインスタンスを再び試みます。</span><span class="sxs-lookup"><span data-stu-id="a7931-246">After a backoff period, it will try the instance again.</span></span> <span data-ttu-id="a7931-247">連続した障害の数など、さまざまな条件に基づいてサーキット ブレーカーを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a7931-247">You can configure the circuit breaker based on various criteria, such as the number of consecutive failures,</span></span>  

- <span data-ttu-id="a7931-248">サービス メッシュは、要求の量、待機時間、エラー率と成功率、応答のサイズなど、サービス間呼び出しに関するメトリックをキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="a7931-248">Service mesh captures metrics about interservice calls, such as the request volume, latency, error and success rates, and response sizes.</span></span> <span data-ttu-id="a7931-249">サービス メッシュでは、要求内の各ホップの相関関係情報を追加することで分散トレースも可能です。</span><span class="sxs-lookup"><span data-stu-id="a7931-249">The service mesh also enables distributed tracing by adding correlation information for each hop in a request.</span></span>

- <span data-ttu-id="a7931-250">サービス間呼び出しに対する相互 TLS 認証。</span><span class="sxs-lookup"><span data-stu-id="a7931-250">Mutual TLS Authentication for service-to-service calls.</span></span>

<span data-ttu-id="a7931-251">サービス メッシュが必要だとお考えですか。</span><span class="sxs-lookup"><span data-stu-id="a7931-251">Do you need a service mesh?</span></span> <span data-ttu-id="a7931-252">たしかに、サービス メッシュによって分散システムにもたらされる価値には説得力があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-252">The value they add to a distributed system is certainly compelling.</span></span> <span data-ttu-id="a7931-253">サービス メッシュを使わない場合は、この記事の最初で説明した各課題をユーザー自身が考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-253">If you don't have a service mesh, you will need to consider each of the challenges mentioned at the beginning of the article.</span></span> <span data-ttu-id="a7931-254">サービス メッシュがなくても再試行、サーキット ブレーカー、分散トレースなどの問題を解決することはできますが、サービス メッシュはこれらの問題を個々のサービスから専用のレイヤーに移してくれます。</span><span class="sxs-lookup"><span data-stu-id="a7931-254">You can solve problems like retry, circuit breaker, and distributed tracing without a service mesh, but a service mesh moves these concerns out of the individual services and into a dedicated layer.</span></span> <span data-ttu-id="a7931-255">その一方で、サービス メッシュはまだ成熟していない比較的新しいテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="a7931-255">On the other hand, service meshes are a relatively new technology that is still maturing.</span></span> <span data-ttu-id="a7931-256">サービス メッシュを展開すると、クラスターのセットアップと構成が複雑になります。</span><span class="sxs-lookup"><span data-stu-id="a7931-256">Deploying a service mesh adds complexity to the setup and configuration of the cluster.</span></span> <span data-ttu-id="a7931-257">要求はサービス メッシュ プロキシを通してルーティングされるようになり、クラスターの各ノードで実行されるサービスが増えるので、パフォーマンスに影響する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-257">There may be performance implications, because requests now get routed through the service mesh proxy, and because extra services are now running on every node in the cluster.</span></span> <span data-ttu-id="a7931-258">運用環境にサービス メッシュを展開する前に、パフォーマンスと負荷のテストを徹底的に行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7931-258">You should do thorough performance and load testing before deploying a service mesh in production.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a7931-259">次の手順</span><span class="sxs-lookup"><span data-stu-id="a7931-259">Next steps</span></span>

<span data-ttu-id="a7931-260">互いに直接通信するマイクロサービスの場合、適切に設計された API を作成することが重要です。</span><span class="sxs-lookup"><span data-stu-id="a7931-260">For microservices that talk directly to each other, it's important to create well-designed APIs.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="a7931-261">API 設計</span><span class="sxs-lookup"><span data-stu-id="a7931-261">API design</span></span>](./api-design.md)