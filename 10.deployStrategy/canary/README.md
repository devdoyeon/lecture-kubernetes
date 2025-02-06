 1.  현재 운영 중인 nginx:1.14 Deployment를 정의 (nginx-deploy.yaml). 
 2.  새로운 nginx:1.15 버전을 Canary 방식으로 일부 배포 
 (nginx-canary-deploy.yaml). 
 3.  Canary 파드의 상태를 모니터링. 
 4.  Canary 테스트가 성공적이면 기존 Deployment를 nginx:1.15로 업데이트. 
 5.  Canary Deployment를 삭제. 
