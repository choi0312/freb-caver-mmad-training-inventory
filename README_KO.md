# FREB-CAVER MMAD 학습 데이터 식별 보고서

작성일: 2026-08-26  
범위: FREB의 GRAFT/CAVER 학습 및 검증에 사용된 MMAD 매니페스트. 잠금 holdout 1,120행은 학습 데이터가 아니므로 원문을 묶음에 포함하지 않았다.

## 핵심 결론

- 원천 데이터는 Hugging Face `jiang-cc/MMAD`의 고정 revision `c4ed190dcb530f2f673ab293e575ad32054bb3cf`이다.
- MMAD 원본 `mmad.json`의 SHA-256은 `639343b491bc67b2abb3c5d719f221ce27f83b2ed97948f4e88055aaa31f1c1e`이다.
- GRAFT 학습 매니페스트는 5,600행이며 GoodsAD, MVTec-AD, MVTec-LOCO, VisA를 각각 1,400행(25%)씩 사용한다.
- 검증 매니페스트는 560행이며 네 출처를 각각 140행(25%)씩 사용한다. 검증 데이터는 학습 가중치 업데이트용이 아니라 모델 선택/게이트용이다.
- CAVER는 5,600행을 그대로 매 스텝 순회하지 않는다. 그중 Anomaly Detection 행으로 515개 same-anchor 쌍을 만들고 2회 반복하여 1,030 optimizer step을 수행한다. 또한 4 step마다 비-AD 안전성 KL 표본 1개를 사용하여 총 258개 표본을 소비한다.
- 정확한 원행은 `sample_id`와 `manifests/teacher_train.jsonl`의 줄 번호로 찾을 수 있다. CAVER의 실제 선택 순서는 `caver_selection/` 두 파일에 기록했다.

## 묶음 파일 안내

| 파일 | 역할 |
| --- | --- |
| `manifests/teacher_train.jsonl` | GRAFT 학습의 원본 5,600행. FREB 데이터 식별의 기준 파일 |
| `manifests/teacher_validation.jsonl` | 검증/모델 선택용 560행; 학습 split과 구분해야 함 |
| `manifests/data_identity.json` | 원 프로젝트가 기록한 행 수·해시·자산 수 메타데이터 |
| `caver_selection/caver_same_anchor_pairs.jsonl` | CAVER 1개 epoch의 515쌍 순서, 원행 줄 번호, 유효 anchor, 2개 epoch의 optimizer step |
| `caver_selection/caver_non_ad_safety_rows.jsonl` | CAVER 전체 1,030 step 중 실제 사용된 258개 비-AD 안전성 표본 |
| `summary/manifest_summary.json` | 출처·범주·과제·라벨·자산·CAVER 선택 통계의 기계 판독형 요약 |
| `SHA256SUMS.txt` | 묶음 내부 파일의 무결성 확인값 |

## MMAD 구성

| MMAD 출처 | 학습 행 | 학습 비중 | 검증 행 | 학습 범주 수 | query 경로 접두사 |
| --- | --- | --- | --- | --- | --- |
| GoodsAD | 1,400 | 25.00% | 140 | 6 | GoodsAD/ |
| MVTec-AD | 1,400 | 25.00% | 140 | 15 | DS-MVTec/ 1,383행 + MVTec-AD/ 17행 |
| MVTec-LOCO | 1,400 | 25.00% | 140 | 5 | MVTec-LOCO/ |
| VisA | 1,400 | 25.00% | 140 | 12 | VisA/ |

주의: 정규화된 출처명은 `MVTec-AD`이지만 query 경로는 MMAD 직렬화 규칙 때문에 대부분 `DS-MVTec/`로 저장되어 있다. 정상 reference 경로는 `MVTec-AD/`이다. 이를 서로 다른 다섯 번째 데이터셋으로 세면 안 된다.

## 과제 유형

| question_type | 학습 행 | 학습 비중 | 검증 행 |
| --- | --- | --- | --- |
| Anomaly Detection | 800 | 14.29% | 80 |
| Defect Analysis | 800 | 14.29% | 80 |
| Defect Classification | 800 | 14.29% | 80 |
| Defect Description | 800 | 14.29% | 80 |
| Defect Localization | 800 | 14.29% | 80 |
| Object Analysis | 800 | 14.29% | 80 |
| Object Classification | 800 | 14.29% | 80 |

학습 라벨은 anomaly 3,692행(65.93%), normal 1,908행(34.07%)이다. 검증 라벨은 anomaly 372행(66.43%), normal 188행(33.57%)이다.

## 범주별 행 수

| MMAD 출처 | category | 학습 행 | 검증 행 |
| --- | --- | --- | --- |
| GoodsAD | cigarette_box | 232 | 8 |
| GoodsAD | drink_bottle | 342 | 47 |
| GoodsAD | drink_can | 134 | 2 |
| GoodsAD | food_bottle | 266 | 37 |
| GoodsAD | food_box | 194 | 15 |
| GoodsAD | food_package | 232 | 31 |
| MVTec-AD | bottle | 62 | 2 |
| MVTec-AD | cable | 134 | 7 |
| MVTec-AD | capsule | 117 | 7 |
| MVTec-AD | carpet | 98 | 7 |
| MVTec-AD | grid | 72 | 11 |
| MVTec-AD | hazelnut | 91 | 6 |
| MVTec-AD | leather | 93 | 7 |
| MVTec-AD | metal_nut | 71 | 13 |
| MVTec-AD | pill | 133 | 11 |
| MVTec-AD | screw | 107 | 26 |
| MVTec-AD | tile | 96 | 14 |
| MVTec-AD | toothbrush | 38 | 0 |
| MVTec-AD | transistor | 93 | 10 |
| MVTec-AD | wood | 59 | 6 |
| MVTec-AD | zipper | 136 | 13 |
| MVTec-LOCO | breakfast_box | 244 | 21 |
| MVTec-LOCO | juice_bottle | 324 | 21 |
| MVTec-LOCO | pushpins | 250 | 41 |
| MVTec-LOCO | screw_bag | 323 | 19 |
| MVTec-LOCO | splicing_connectors | 259 | 38 |
| VisA | candle | 126 | 6 |
| VisA | capsules | 89 | 9 |
| VisA | cashew | 111 | 9 |
| VisA | chewinggum | 95 | 11 |
| VisA | fryum | 105 | 5 |
| VisA | macaroni1 | 131 | 15 |
| VisA | macaroni2 | 140 | 16 |
| VisA | pcb1 | 124 | 23 |
| VisA | pcb2 | 118 | 8 |
| VisA | pcb3 | 122 | 14 |
| VisA | pcb4 | 150 | 11 |
| VisA | pipe_fryum | 89 | 13 |

## 이미지 자산 단위

- 학습: query 고유 4,044개, reference 고유 3,301개, 합집합 고유 7,345개
- 검증: query 고유 380개, reference 고유 345개, 합집합 고유 725개
- 학습+검증 합집합: 고유 자산 8,070개 (`data_identity.json` 기록)
- 이 묶음은 매니페스트와 경로만 포함한다. 실제 이미지 바이트는 용량과 원 데이터 배포 조건 때문에 포함하지 않았다.

## CAVER가 실제 사용한 학습 표본

- same-anchor pair: epoch당 515개, 2 epochs, 총 1,030 optimizer steps
- pair에서 고유 anomaly 원행: 492개
- pair에서 고유 normal 원행: 308개
- pair에 닿는 고유 원행 합집합: 800개
- normal-null: epoch당 515개 합성 행. anomaly 질문/선택지를 유지하되 query와 reference를 같은 정상 anchor 이미지로 바꾸고 정답을 의미상 `No`인 선택지로 설정한다.
- 비-AD safety KL: 258회, 고유 원행 258개
- CAVER 전체에서 닿는 고유 원본 학습 `sample_id`: 1,058개

| MMAD 출처 | same-anchor pair/epoch | 비-AD safety event |
| --- | --- | --- |
| GoodsAD | 121 | 73 |
| MVTec-AD | 137 | 64 |
| MVTec-LOCO | 134 | 60 |
| VisA | 123 | 61 |

## 한 행을 식별하는 방법

`teacher_train.jsonl`의 각 행은 독립 JSON 객체이며 다음 필드를 우선 확인한다.

1. `sample_id`: 매니페스트 내부 고유 식별자
2. `source` / `category`: 정규화된 MMAD 하위 데이터셋과 범주
3. `question_type`: 7개 과제 중 하나
4. `label`: `anomaly` 또는 `normal`
5. `image`: query 이미지의 MMAD revision 내부 상대 경로
6. `template_image`: 정상 reference 이미지의 상대 경로
7. `question`, `options`, `correct_answer`: 실제 감독 QA 내용
8. `teacher_answer`, `teacher_segmentation`, `teacher_thinking`: 캐시된 teacher 감독 신호

`caver_same_anchor_pairs.jsonl`의 `*_manifest_line`은 원본 `teacher_train.jsonl`의 1-based 줄 번호다. `anomaly_original_reference_image`와 `anomaly_effective_reference_image`가 다를 수 있는데, CAVER가 normal 쪽 reference를 동일 anchor로 강제하기 때문이다.

## 재현성/무결성

| 역할 | 행 수 | SHA-256 |
| --- | ---: | --- |
| GRAFT/CAVER 기반 학습 매니페스트 | 5,600 | `c55e911cb8dcd4aede4e3ab93cf111228624987ff385e295fced7c66cf4eb235` |
| 검증 매니페스트 | 560 | `11bd4223ddf6cbccd9416678aa9ae1522228e7236c40b0cb705bff2834066ed0` |
| 잠금 holdout (학습 아님, 원문 미포함) | 1,120 | `ba0cd97dbc9c17395bd0d471db17dcf1ec83428eb9ad5ae9b8555f703233f7ec` |

위 학습·검증 해시는 FREB 공개 설정과 로컬 sealed input의 기록이 일치한다. 따라서 이 묶음의 원행 목록은 공개 설정이 지칭한 데이터와 동일한 identity를 갖는다.

## 근거 링크

- FREB-CAVER repository: https://github.com/choi0312/FREB-CAVER
- FREB public configuration: https://github.com/choi0312/FREB-CAVER/blob/main/configs/caver_stage4b_preregistration.json
- FREB reproducibility notes: https://github.com/choi0312/FREB-CAVER/blob/main/REPRODUCIBILITY.md
- JUDO repository: https://github.com/woodavid31/JUDO
- MMAD fixed revision: https://huggingface.co/datasets/jiang-cc/MMAD/tree/c4ed190dcb530f2f673ab293e575ad32054bb3cf

## 해석 시 주의

- `teacher_validation.jsonl`과 `holdout.jsonl`을 학습량에 합산하면 안 된다.
- CAVER의 1,030 step은 1,030개의 서로 다른 pair가 아니라 515개 pair를 2회 반복한 값이다.
- normal-null은 원본 MMAD QA 행이 아니라 동일 정상 reference로부터 런타임에 합성된 반사실적 행이다.
- JSONL에는 이미지 경로와 QA가 들어 있으며 실제 이미지 파일은 MMAD 고정 revision에서 별도로 확보해야 한다.
