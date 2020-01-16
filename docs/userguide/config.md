# Configuration

설정의 중요 구성요소는 Zone, NodePool, Monitor이다.

- **Zone** - 개별 도메인 서비스 구성이다.
- **NodePool** - 서버 집합으로 가용 자원풀을 의미한다.
- **Monitor** - NodePool의 Health Monitoring을 통해 Zone의 A Records를 구성한다.

![](compass_02.png)

기타 구성요소는 다음과 같다.

- **Forwarder** -  미등록 또는 장애 도메인 Query를 외부 DNS로 포워딩한다.
- **Replication** - HA(High Availability)를 위한 복제세트를 구성한다.



## 설정 구조

설정 파일 경로는 `/etc/compass/compass.conf` 이다. JSON형식이며 아래와 같은 구조를 가진다.

```xml
# /etc/compass.conf

{
  "Version: "2019-12-23",
  "NodePools" : [ ],
  "Zones" : [ ],
  "Monitors" : [ ],
  "Forwarders" : { },
  "Replication" : { }
}
```

- `Version` - 이 값은 반드시 `"2019-12-23"` 이어야 한다. 날짜를 버전으로 사용한다. 
- `Zones`, `Nodepools`, `Monitors` - 각 구성요소의 멀티 설정 (아래 상세설명)
- `Forwarders` - 포워딩할 외부 DNS 설정
- `Replication` - 복제 설정



## NodePool

NodePool은 동일한 목적을 가진 서버들의 집합을 가리킨다. 관리자는 NodePool 감시에 적합한 Monitor를 바인딩한다.

```json
"NodePools" : [
	{
        "Name" : "myPool",
        "Description" : "this is the first pool",
        "LoadBalancingMode" : "RoundRobin",
        "MinimumMembers" : {
          "Enabled" : true,
          "Count" : 1,
          "Action" : "failover"
        },
        "Members" : [
          {
            "Enabled" : true,
            "Address" : "10.12.10.7"
          },
          {
            "Enabled" : true,
            "Address" : "10.12.10.8"
          }
        ],
        "Monitors" : [ "icmp" ]
    }, {
        "Name" : "secondPool",
        ... (생략) ...
    }    
],
```

| 키                 | 설명                                                         | 기본 값                                           |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------- |
| `Name`             | 이름                                                         | (필수)                                            |
| `Description`      | 설명                                                         | (옵션)                                            |
| `LoadBalacingMode` | 로드 밸런싱 모드                                             | RoundRobin                                        |
| `MinimumMembers`   | 최소 구성 멤버 설정. 가용한 멤버 수가 `count` 미만인 경우 `action`을 수행한다. | Enabled: true<br />Count: 1<br />Action: failover |
| `Members`          | 구성 서버 목록                                               | Enabled: true<br />Address: (필수)                |
| `Monitors`         | 감시 모니터                                                  | (옵션)                                            |

