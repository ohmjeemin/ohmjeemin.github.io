---
title: "NodeJS에서 SEED128 암호화 적용 시 error:0308010C:digital envelope routines::unsupported 발생"
date: 2024-12-28 10:00:00 +/- TTTT
categories: [Dev, NodeJS]
tags: [NodeJS]
pin: true
---

## 문제 발생

우체국 택배 송장 발급 기능을 구현하기 위해 우체국 Open API를 사용하던 중, 송장 발급 API 요청 시 데이터를 SEED128 알고리즘으로 암호화해야 했습니다.  
하지만 암호화 코드를 실행했을 때, 아래와 같은 에러가 발생하며 정상적으로 작동하지 않았습니다.

```javascript
Error: Error: error:0308010C:digital envelope routines::unsupported
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

이 오류는 Node.js 17 이상 버전에서 기본으로 사용하는 OpenSSL 3와의 호환성 문제로 발생합니다. OpenSSL 3에서는 일부 암호화 알고리즘, 특히 오래된 알고리즘(레거시 알고리즘)이 제한되거나 비활성화되었습니다.
SEED128 알고리즘은 OpenSSL에서 레거시 알고리즘으로 간주되어 지원되지 않아 이 에러가 발생한 것입니다.

OpenSSL: 네트워크 데이터 통신을 위한 TLS/SSL 프로토콜의 오픈소스 구현
Node.js 버전: 20

## 해결방안

NODE_OPTIONS 환경 변수에 `--openssl-legacy-provider`를 추가하여 레거시 알고리즘 지원을 활성화하면 문제를 해결할 수 있습니다.  
해당 옵션을 적용한 결과, 암호화가 정상적으로 작동하는 것을 확인했습니다.
