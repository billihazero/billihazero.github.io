---
title: Prisma + Timestamp 날짜 조건 검색하기
categories:
  - DEV

tags:
  - Prisma
  - Timezone

photos:
  - /post_images/prisma_datetime.png
---

대부분 DB 테이블에 필수로 들어가는 컬럼 "createdAt" 이 있습니다. 이 컬럼은 보통 Timestamp 타입으로 저장되며, 기본적으로 UTC를 기준으로 기록됩니다.

Prisma는 timezone 설정을 따로 지원하지 않기 때문에, 날짜 조건을 이용한 조회를 구현할 때 고민이 필요했습니다.

---

## 문제상황

예를 들어, "2026-03-20의 하루 데이터" 를 조회한다고 하면, 이 기준은 한국 시간(KST) 기준입니다. 하지만 DB에는 시간이 UTC 기준으로 저장되어 있기 때문에, 단순히 "2026-03-20" 을 그대로 비교하면 원하는 결과가 나오지 않습니다.

```
KST 기준 하루 : 2026-03-20 00:00:00 ~ 2026-03-21 00:00:00 (KST)

UTC로 보았을 때 : 2026-03-19 15:00:00 ~ 2026-03-20 15:00:00 (UTC)

```

하루의 기준이 달라집니다.

## 해결방법

그래서 where 조건으로 날짜를 넣을 때, KST 기준 하루 범위를 만든 후 이를 Date 객체로 변환하여 넣는 방식으로 처리 하였습니다.

```
import dayjs from 'dayjs';
import utc from 'dayjs/plugin/utc';
import tz from 'dayjs/plugin/timezone';

dayjs.extend(utc);
dayjs.extend(tz);

const KST = 'Asia/Seoul';

export function kstDayRange(target) {
  const base = target ? dayjs.tz(target, KST) : dayjs().tz(KST);
  const start = base.startOf('day');
  const end = start.add(1, 'day');

  return {
    startDate: start.toDate(),
    endDate: end.toDate(),
  };
}

```

그리고 Prisma에서는 이렇게 사용하였습니다.

```
const { startDate, endDate } = kstDayRange('2026-03-20');
const result = await prisma.post.findMany({
    where: {
        createdAt: {
            gte: startDate,
            lt: endDate,
        },
    },
});
```

## 구현 포인트

- 날짜 기준은 KST로 계산합니다.
- 실제 비교는 UTC기반 Date 객체로 수행합니다.
  - 문자열 포맷으로 바꿀 경우, timezone 정보가 없기 때문에 Date 객체로 변환하여 사용

## 번외) Timezone 지원 ORM

### 1. TypeORM

```
TypeOrmModule.forRoot({
  type: 'mysql',
  timezone: 'Z',
})

```

- DB 연결 시 timezone 지정이 가능합니다.
- 조회할 때 자동 변환되어 조회됩니다.

### 2. Sequelize

```
new Sequelize(db, user, pass, {
  timezone: '+09:00'
});
```

- 저장/조회 시 timezone 적용이 가능합니다.
  - 저장 시 timezone을 적용하면, +09:00 기준으로 해석해서 DB에 저장됩니다.

## Reference

https://github.com/prisma/prisma/discussions/15329
