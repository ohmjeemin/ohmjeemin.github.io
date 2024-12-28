---
title: "NodeJS에서 SEED128 암호화 적용 시 error:0308010C:digital envelope routines::unsupported 발생"
date: 2024-12-28 10:00:00 +/- TTTT
categories: [Dev, NodeJS]
tags: [NodeJS]
pin: true
---

## 문제 발생

우체국 택배 송장 발급 기능을 추가하기 위해 우체국 open api를 적용하고 있었다.
송장 발급 api 요청 시 데이터를 SEED128 암호화해서 보내야 하기 때문에 SEED128 암호화 코드를 실행시켰더니,
다음과 같은 에러가 발생하며 정상 작동하지 않았다.

```Error: Error: error:0308010C:digital envelope routines::unsupported
    at Cipheriv.createCipherBase (node:internal/crypto/cipher:121:19)
    at Cipheriv.createCipherWithIV (node:internal/crypto/cipher:133:3)
    at new Cipheriv (node:internal/crypto/cipher:234:3)
    at createCipheriv (node:crypto:143:10)
    at SEED128Service.seedEncrypt (/Users/min/dev/esim-crm-api/src/orders/seed128.ts:28:38)
    at bootstrap (/Users/min/dev/esim-crm-api/src/main.ts:67:46)
    at processTicksAndRejections (node:internal/process/task_queues:105:5) {
  library: 'digital envelope routines',
  reason: 'unsupported',
  code: 'ERR_OSSL_EVP_UNSUPPORTED'
}
```

## 원인

`error:0308010C:digital envelope routines::unsupported` 오류는 주로 Node.js 버전과 사용 중인 암호화 알고리즘 간의 호환성 문제에서 발생한다. 이 문제는 Node.js 17 이상 버전에서 OpenSSL 3가 기본으로 사용되면서, 일부 암호화 알고리즘(특히 오래된 알고리즘) 지원이 제한되거나 비활성화된 경우에 발생한 것이였다. 내가 사용하려한 SEED128 알고리즘이 레거시로 판단된 것이다.

- openSSL: 네트워크를 통한 데이터 통신을 할 때 쓰이는 프로토콜인 TLS와 SSL을 오픈소스로 해놓은 것
- 내 node 버전: 20

## 해결방안

NODE_OPTIONS 환경 변수를 --openssl-legacy-provider로 설정하면 레거시 알고리즘도 공급되도록 변경된다.
적용했더니 정상적으로 작동한 것을 확인했다.
