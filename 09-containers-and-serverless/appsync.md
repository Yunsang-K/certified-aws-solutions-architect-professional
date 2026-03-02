# AWS AppSync

* AppSync는 *GraphQL*을 사용하는 완전관리형(Managed) 서비스이다.
* *GraphQL* 기반 API를 AWS에서 구축할 때 사용한다.
* *GraphQL*은 애플리케이션이 **필요한 데이터만 정확히** 가져오도록 해주며, **여러 데이터 소스의 데이터를 조합**해 단일 요청으로 조회하기 쉽게 만든다.
* *GraphQL* 뒤의 데이터 소스(datasets)는 다음과 같은 것들을 포함할 수 있다:

  * NoSQL 데이터 스토어
  * RDS 데이터베이스
  * HTTP API
  * 기타 등등
* AppSync는 (리졸버를 통해) DynamoDB, Aurora, Elasticsearch 등과 통합된다.
* 고객(사용자) 정의 리소스는 Lambda를 통해 지원할 수 있다.
* WebSocket 또는 MQTT over WebSocket 프로토콜을 사용한 **실시간(Real-time) 데이터 조회**를 지원한다.
* 모바일 애플리케이션 관점에서 Cognito Sync의 대체로 활용될 수 있다.
* 시작하려면 GraphQL 스키마가 필요하다.
* GraphQL 스키마 예시:

```graphql
type Query {
    human(id: ID!): Human
}

type Human {
    name: String
    appearsIn: [Episode]
    starships: [Starship]
}

enum Episode {
    NEWHOPE
    EMPIRE
    JEDI
}

type Starship {
    name: String
}
```

## 보안(Security)

* AppSync에 애플리케이션이 접근하도록 인증/인가(Authorization)하는 방식은 4가지가 있다:

  * API_KEY
  * AWS_IAM
  * OPENID_CONNECT (OpenID Connect Provider / JWT)
  * AMAZON_COGNITO_USER_POOLS
* 커스텀 도메인 및 HTTPS 구성이 필요하면, AppSync 앞단에 CloudFront를 두는 방식을 사용한다.
