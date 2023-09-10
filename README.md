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

## 문제 해결
- 특정 시간(예를 들어 회의실 예약 1시간 전, 설문조사 마감 1시간 전 등)에 예약 알림을 주는 기능을 구현하려고 했고, 이를 위해 agenda 스케줄러를 선택했습니다. agenda 스케줄러를 선택한 이유는 크게 두 가지였습니다. 첫째로, Mongodb 서버와의 통합을 통해 스케줄링 작업을 데이터베이스에 저장하고 추적하기 용이하게 만들수 있었습니다. 둘째로, 서버를 재시작하더라도 실행 중인 스케줄러가 유지된다는 점이 소개되어 있었습니다. 그러나 실제로 서버를 재시작하는 테스트를 진행하면서 스케줄러가 동작하지 않는 문제점을 발견했습니다. 이 문제의 원인을 파악하기 위해 스케줄러 등록 코드를 변경하거나 agenda 자체 옵션을 조절해보았지만 해결되지 않았습니다. 이 문제가 사용자들 사이에서 공통적으로 발생하고 있다는 것을 알게되었고, agenda 스케줄러의 라이브러리 기능으로는 서버 재시작 시 스케줄러가 유지되지 않는다는 사실을 알게 되었습니다. 이에 따라 저는 다른 방식으로 문제에 접근하기로 결정했습니다. 서버가 재시작될 때 아직 예정된 스케줄러만을 다시 동작시키는 방법을 고려하였고, agendaJobs의 data 컬럼에 스케줄링 작업에 대한 자세한 정보와 동작 내용을 기록하였습니다. 그런 다음, 서버가 재시작될 때 agendaJobs의 lastfinishedAt 값이 null인 (아직 실행되지 않은) 스케줄러들을 식별하고, 해당 스케줄러들에 대해서 data 컬럼을 기반으로 스케줄러를 다시 실행하도록 구현했습니다. 이렇게 함으로써 서버가 재시작되더라도 스케줄러들이 정상적으로 작동할 수 있도록 구현했습니다.
- danfojs를 사용하여 엑셀 데이터 처리를 수행했습니다. 이 결정을 내리게 된 이유는 제가 파이썬에서 데이터 핸들링을 위해 주로 사용하는 라이브러리인 pandas와 유사한 기능을 제공하기 때문이었습니다. 그러나 danfojs를 사용하면서 다음과 같은 문제점을 발견했습니다. 첫째로, danfojs는 TensorFlow를 사용하기 때문에 서버를 실행할 때마다 다음과 같은 경고 메시지가 표시되었습니다. "I tensorflow/core/platform/cpu_feature_guard.cc:151] This TensorFlow binary is optimized with oneAPI Deep Neural Network Library (oneDNN) to use the following CPU instructions in performance-critical operations: AVX2 FMA. To enable them in other operations, rebuild TensorFlow with the appropriate compiler flags." 이로 인해 danfojs를 사용할 때 시스템의 CPU 기능을 최적으로 활용하지 못할 수 있다는 우려가 있었습니다. 둘째로, danfojs 라이브러리 자체가 38.5MB로 상당히 무거웠습니다. 이로 인해 프로젝트의 용량이 불필요하게 커지고, 성능에도 영향을 미칠 수 있었습니다. 셋째로 csv utf-8 형식의 파일에 대해 인코딩을 처리하는 데 어려움을 겪었습니다. 이로 인해 데이터의 인코딩 문제를 처리하는 데 추가적인 작업이 필요했습니다. 위와 같은 이유로 danfojs 대신 xlsx 라이브러리로 전환 작업을 진행하였습니다. xlsx는 tensorflow를 사용하지 않았고, 라이브러리가 7.5MB로 가벼웠고, 인코딩 처리를 자동으로 수행해주어서 danfojs와는 다른 방식으로 구현하였음에도 불구하고 위의 문제를 모두 해결할 수 있었습니다. 이러한 경험을 토대로 라이브러리 선택에 대한 중요한 고려 사항을 배웠으며, 프로젝트의 성능과 효율성을 높이기 위해 항상 적절한 도구를 선택해야 한다는 것을 깨달았습니다.





