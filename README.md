## 프로젝트 소개
- 건물 관리자와 입주사 직원들의 소통을 위한 건물편의 앱/웹
- 건물 유형과 기능에 따라 모듈화한 B2B SaaS 모델
- 공지, 일정, 민원, 설문조사, 입주카드, 공용시설, 관리비, 임직원 관리 기능 등으로 구성
- 6개의 건물, 약 1600명의 사용자가 사용하는 플랫폼

## 사용 기술
Backend
- Language: javascript(es6), typescript
- Framework: Express.js
- Database: MongoDB (Mongoose)
- Etc: git, nginx
  
Devops
- AWS (ec2, route53, s3, codecommit, cloudwatch)

Collaboration
- Firebase token, FCM

## 위기극복 및 해결방법
- 특정 시간(예를 들어 회의실 예약 1시간 전, 설문조사 마감 1시간 전 등)에 예약 알림을 주는 기능을 구현하려고 했고, 이를 위해 agenda 스케줄러를 선택했습니다. agenda 스케줄러를 선택한 이유는 크게 두 가지였습니다. 첫째로, Mongodb 서버와의 통합을 통해 스케줄링 작업을 데이터베이스에 저장하고 추적하기 용이하게 만들수 있었습니다. 둘째로, 서버를 재시작하더라도 실행 중인 스케줄러가 유지된다는 점이 소개되어 있었습니다. 그러나 실제로 서버를 재시작하는 테스트를 진행하면서 스케줄러가 동작하지 않는 문제점을 발견했습니다. 이 문제의 원인을 파악하기 위해 스케줄러 등록 코드를 변경하거나 agenda 자체 옵션을 조절해보았지만 해결되지 않았습니다. 이 문제가 사용자들 사이에서 공통적으로 발생하고 있다는 것을 알게되었고, agenda 스케줄러의 라이브러리 기능으로는 서버 재시작 시 스케줄러가 유지되지 않는다는 사실을 알게 되었습니다. 이에 따라 저는 다른 방식으로 문제에 접근하기로 결정했습니다. 서버가 재시작될 때 아직 예정된 스케줄러만을 다시 동작시키는 방법을 고려하였고, agendaJobs의 data 컬럼에 스케줄링 작업에 대한 자세한 정보와 동작 내용을 기록하였습니다. 그런 다음, 서버가 재시작될 때 agendaJobs의 lastfinishedAt 값이 null인 (아직 실행되지 않은) 스케줄러들을 식별하고, 해당 스케줄러들에 대해서 "data" 컬럼을 기반으로 스케줄러를 다시 실행하도록 구현했습니다. 이렇게 함으로써 서버가 재시작되더라도 스케줄러들이 정상적으로 작동할 수 있도록 구현했습니다.
