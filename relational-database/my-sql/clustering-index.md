# Clustering index

MySQL에서 클러스터링 인덱스는 InnoDB와 TokuDB 스토리지 엔진에서만 지원하며, 나머지 스토리지 엔진에서는 지원되지 않습니다.

### Clustered Index

Clustered Index는 테이블의 프라이머리 키에 대해서만 적용되는 내용입니다. 즉 프라이머리 키값이 비슷한 레코드끼리 묶어서 저장하는 것을 Clustered Index라고 표현합니다(Cluster, 무리를 이루다).

여기에서 중요한 것은 프라이머리 키값에 의해 레코드의 저장 위치가 결정된다는 것입니다. 또한 프라이머리 키값이 변경된다면 그 레코드의 물리적인 저장 위치가 바뀌어야 한다는 것을 의미하기도 합니다. 프라이머리 키값으로 클러스터링된 테이블은 프라이머리 키값 자체에 대한 의존도가 상당히 크기 때문에 신중히 프라이머리 키를 결정해야 합니다.

Clustered Index는 프라이머리 키값에 의해 레코드의 저장 위치가 결정되므로 사실 인덱스 알고리즘이라기보다 테이블 레코드의 저장 방식이라고 볼 수도 있습니다. 그래서 “클러스터링 인덱스”와 “클러스터 테이블”은 동의어로 사용되기도 합니다. 또한 클러스터링의 기준이 되는 프라이머리 키는 클러스터 키라고도 표현합니다. 일반적으로 InnoDB와 같이 항상 Clustered Index로 저장되는 테이블은 프라이머리 키 기반의 검색이 매우 빠르며, 대신 레코드의 저장이나 프라이머리 키의 변경이 상대적으로 느릴 수 밖에 없습니다.

![https://velog.velcdn.com/images/eastshine-high/post/1aecd1c1-f5b6-4204-8fe4-c787fa283667/image.png](https://velog.velcdn.com/images/eastshine-high/post/1aecd1c1-f5b6-4204-8fe4-c787fa283667/image.png)

Clustered Index 구조를 보면 클러스터링 테이블의 구조 자체는 일반 B-Tree와 많이 비슷하게 닮아 있습니다. 하지만 B-Tree의 리프 노드와는 달리 Clustered Index의 리프노드에는 레코드의 모든 칼럼이 같이 저장됩니다. 즉 클러스터링 테이블은 그 자체가 하나의 거대한 인덱스 구조로 관리됩니다.

### Non-Clustered Index

Secondary Index(보조 인덱스) 라고도 부릅니다. 인덱스 안에 데이터가 포함되지 않습니다. 클러스터링 인덱스와 달리 테이블에 여러개가 존재할 수 있으며 Unique 제약조건으로 컬럼을 생성하면 자동으로 생성됩니다. 아래 InnoDB의 Non-Clustered Index 구조를 보시면, 실제 레코드가 저장된 주소가 아닌 프라이머리 키 값을 저장하고 있습니다.

![http://dl.dropbox.com/s/gbkk3trrm4epl2o/non-clustered-index.png](http://dl.dropbox.com/s/gbkk3trrm4epl2o/non-clustered-index.png)

만약 Non-Clustered Index에서 실제 레코드가 저장된 주소를 갖고 있으면, 클러스터 인덱스의 키값이 변경될 때마다 데이터 레코드의 주소가 변경되고 그때마다 해당 테이블의 모든 인덱스에 저장된 주소 값을 변경해야 합니다. 이런 번거로움을 방지하고자 InnoDB 테이블(클러스터 테이블)의 모든 보조 인덱스는 해당 레코드가 저장된 주소가 아니라 프라이머리 키값을 저장하도록 구현돼 있습니다.

따라서 Non-Clustered Index는 Clustered Index 와 비교해서 조회 속도가 약간 느립니다. 대신 Clustered Index보다 Insert, Update, Delete 시 부하가 적습니다. 

---

**참조**

<이성욱, Real MySQL, 위키북스>

[찰리의 인덱싱, 우아한테크코스 테코톡](https://www.youtube.com/watch?v=P5SZaTQnVCA&t=1046s)
