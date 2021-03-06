---
title: Azure Cache for Redis を Azure Spring Cloud アプリケーションにバインドする
description: Azure Cache for Redis を Azure Spring Cloud アプリケーションにバインドする方法について説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6bbd54be46effe324199639477f9ca4ab31bea98
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091404"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Azure Cache for Redis を Azure Spring Cloud アプリケーションにバインドする 

Spring Boot アプリケーションを手動で構成するのではなく、Azure Spring Cloud を使用して、選択した Azure サービスをアプリケーションに自動的にバインドすることができます。 この記事では、アプリケーションを Azure Cache for Redis にバインドする方法について説明します。

## <a name="prerequisites"></a>前提条件

* デプロイ済みの Azure Spring Cloud インスタンス
* Azure Cache for Redis サービス インスタンス
* Azure CLI 用の Azure Spring Cloud 拡張機能

Azure Spring Cloud インスタンスをデプロイしていない場合は、[Azure Spring Cloud アプリのデプロイに関するクイックスタート](spring-cloud-quickstart-launch-app-portal.md)の手順を実行してください。

## <a name="bind-azure-cache-for-redis"></a>Azure Cache for Redis をバインドする

1. プロジェクトの pom.xml ファイルに次の依存関係を追加します。

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. `application.properties` ファイルからすべての `spring.redis.*` プロパティを削除します

1. `az spring-cloud app update` を使用して現在のデプロイを更新するか、`az spring-cloud app deployment create` を使用して新しいデプロイを作成します。

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。 **アプリケーション ダッシュボード**に移動し、Azure Cache for Redis にバインドするアプリケーションを選択します。 このアプリケーションは、前の手順で更新またはデプロイしたものと同じです。

1. **[サービス バインド]** を選択し、 **[サービス バインドの作成]** を選択します。 フォームに入力します。このとき、必ず **[バインドの種類]** 値に **[Azure Cache for Redis]** 、実際の Azure Cache for Redis サーバー、 **[主]** キー オプションを選択します。

1. アプリを再起動します。 これでバインドが機能するようになります。

1. サービスのバインドが正常であることを確認するには、バインド名を選択し、その詳細を確認します。 `property` フィールドはこのようになります。
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>次のステップ

この記事では、Azure Spring Cloud アプリケーションを Azure Cache for Redis にバインドする方法を学習しました。 サービスをアプリケーションにバインドする方法については、[Azure Database for MySQL インスタンスへのバインド](spring-cloud-tutorial-bind-mysql.md)に関するページを参照してください。
