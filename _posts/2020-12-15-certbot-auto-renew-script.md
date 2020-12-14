---
title: certbot 자동 갱신 스크립트
date: 2020-12-15 00:54:01
categories:
  - Linux
tags:
  - Linux
---

AWS Lightsail 에서 certbot 을 이용하여 https를 사용하고 있다.
Let's encrypt 인증서는 90일마다 갱신해줘야해서 귀찮았는데 아래와 같은 스크립트를 찾았다. 실행해보니 일단 잘 작동한다.

crontab에 걸어놓고 인증서가 잘 갱신되는지 확인해봐야겠다.

```shell
#!⁄bin⁄bash
# sudo test
if [[ $(⁄usr⁄bin⁄id -u) -ne 0 ]]; then
  echo "Not running as root"
  exit
fi
# test valid date left.
TESTDATE=`certbot certificates | grep VALID`
ATD=($TESTDATE)
LEFTDATE=`expr ${ATD[5]}`
echo "date left : ${LEFTDATE}"
if [ ${LEFTDATE} -lt 30 ]; then
  echo "Left date less than a month, OK, proceeding update !"
  service apache2 stop
  certbot renew
  service apache2 start
else
  echo "No need to update, yet"
fi
```

이 스크립트의 출처는 [자유로운 그날을 위해](https://rageworx.tistory.com/1926) 이다.
