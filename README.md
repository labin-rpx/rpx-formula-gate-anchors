# RPX 처방 게이트 일일 앵커 (공개)

이 저장소는 [RPX 처방 게이트](https://gate.rpx.co.kr)의 평가 기록(이벤트 해시 체인)이 나중에 몰래 바뀌지 않았다는 것을 **제3자가 직접 확인**할 수 있게 하는 공개 기록입니다. 특허 출원 10-2026-0185990 「인공지능 기반 화장품 처방 후보 평가 시스템 및 방법」, 출원인 주식회사 알피엑스.

여기에는 **해시만** 있고 처방이나 매출 같은 내용은 없습니다.

## 매일 00:10(한국 시간)에 하는 일
1. 전날까지의 워크스페이스별 이벤트 체인 끝(seq, hash), 전날 생성된 리포트 해시들의 머클 루트, 평가 대장 스냅샷 해시를 모아 `anchors/YYYY/MM/DD.json`으로 커밋합니다. 파일 내용은 RFC 8785(JCS) 정규화 문자열이며 `sha256(파일) = digest`입니다.
2. 같은 파일에 대해 공개 시각 인증 기관(FreeTSA)의 **RFC 3161 타임스탬프 토큰**(`.tsr`)을 받아 함께 커밋합니다.
3. **OpenTimestamps** 증명(`.ots`)을 만들어 커밋하고, 다음 날부터 비트코인 블록 확정 증명으로 갱신합니다.

이 저장소는 강제 푸시가 금지되어 있고, 커밋 시각은 GitHub 서버 기록(푸시 이벤트)과 외부 기관 타임스탬프로 교차 확인됩니다.

## 직접 검증하는 방법
```bash
# 1) 파일 해시가 사이트의 앵커 digest와 같은지
sha256sum anchors/2026/10/01.json

# 2) RFC 3161 타임스탬프 검증 (FreeTSA 인증서는 tsa/ 폴더)
openssl ts -verify -in anchors/2026/10/01.json.tsr -data anchors/2026/10/01.json -CAfile tsa/freetsa-cacert.pem -untrusted tsa/freetsa-tsa.crt

# 3) OpenTimestamps(비트코인) 검증
pip install opentimestamps-client
ots verify anchors/2026/10/01.json.ots
```
사이트의 [무결성 검증](https://gate.rpx.co.kr/verify) 화면에서 이벤트 체인을 브라우저로 직접 재계산할 수 있습니다.
