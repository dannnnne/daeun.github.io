---
title: "[ Paper Review ] ALOHA: A(IBoM) tooL generatOr for Hugging fAce"
date: 2026-09-18 00:34:00 +0800
categories: [Paper_Review, AI, BOM]
tags: [Paper_Review, AI, BOM]
---



### Abstract

- AI가 많이 사용됨에 따라 AI 공급망 내의 transparency, security, traceability가 중요
- ALOHA는 Hugging Face에 host된 AI로부터 AIBoM을 생성하는 도구
- CycloneDX를 활용하여 sw transparency와 security 만족
- ground truth 구축 방식 요약
- 연구 결과 요약
- 미래에 도움이 되길 바라며..

### ALOHA

- input : HF에 있는 model ID ( format : author/model )
- ML Component Generator
    - model과 관련된 정보를 찾고 CycloneDX 기준에 맞는 ML component를 생성
        - HF API에 GET rreequest를 보냄
            - model metadata, README.md file을 얻기 위함 (model 설명, 사용 section을 포함)
        - 추출된 정보는 CycloneDX filed에 맞게 매핑되면서 ML Component를 생성
- Dataset Component Generator
    - model dataset이 존재하는지 확인하고, 존재한다면 다음 과정 진행
    - HF APPI에 GET request를 요청하고 dataset metadata를 얻음
    - 관련있는 저보를 추출해서 CycloneDX에 매핑하고 dataset component를 생성
- ML component와 dataset component가 base BoM에 추가되고, JSON 형식의 AIBoM을 생성

---

- model card - README.md에 저장
    - metadata
        - YAML
        - tags, tasks, licenses
    - text description
        - Markdown
        - purpose, performance, limitations
- HF는 template을 제공하지만 이를 지키지 않는 model이 많음 (다운로드 수가 많은 10,000개를 대상으로 진행)
- model 정보는 meatadata 영역에 저장
- dataset 정보는 components 영역에 저장

### ALOHA in Action

`python ALOHA.py <model_id> -o <output_dir_path>`

- model ID : author/model 형식, 필수 parameter
- output_dir_path : 선택 parameter, 작성하지 않으면 default directory 사용
- AIBOM 분석
    - BomFormat : format(CycloneDX)
    - specVersion : specific version
    - serialNumber : AIBoM의 unique identity
    - version : 위와 동일
    - metadata field
        - timestamp : AIBoM의 생성 시간
        - component : AI model에 대한 설명 제공
            - bom-ref : component에 해당하는 고유 참조값
        - external References : BoM에는 포함되지 않는 간접적인 관련 정보 포함
        - ModelCard : AI model의 구체적인 것을 묘사
            - task, architecture, dataaset등 작성
        - properties
            - library와 같은 BybloneDX와 직접적으로 매칭되지 않는 추가 metadat 저장

### Evaluation

- Study Design
    - purpose : data field가 완전하게 채워졌는지
    - RQ : ALOHA에 의해 생성된 AIBoM이 모든 data field에 대해 작성을 하는가?
    - dataset : 적어도 100 downloads, 100 likes
    - 통계적으로 전체를 대표할 수 있는 크기의 무작위 표본을 뽑았고, 그 기준을 신뢰수준 95%, 오차범위 5%로 잡음
        - 모집단 : 1,643 / 표본 : 312
- Results
    - 생성된 AIBoM에서 문서화된 부분과 놓친 부분을 정리함
    - tags, license, task 등 90% 이상 작성되었지만 1% 미만으로 작성된 case도 존재
    - 많이 작성되는 부분은 저자에 의해 작성될 수 있는 부분이거나 HF의 manifest/configuration file을 통해 작성할 수 있는 부분
    - 놓친 data field를 분석함
    - 놓친 이유를 code를 활용하여 3가지로 나눔
        - code 1 : missing information
            - model card에 정보가 없는 것
        - code 2 : misplaced information
            - 매핑되지 않는 data field에 정보가 작성된 경우
        - code 3 : restricted access to information
            - model card에 대한 접근이 어떠한 조건을 만족하지 않으면 허락되지 않는 경우
    - 결과
        - 77.62%는 code 1, model card에 정보가 없음
        - 21.56%는 code 2, 관련된 정보가 model card의 다른 section에 적힘
    
### Discussion and Limitation
    
- ALOHA의 output의 성능은 input에 의존함
- AI model 개발자가 정보를 제공하지 않으면 그만큼 quality가 떨어짐
    
### Threats to Validity

- 생략
    
### Conclusion and Future Work
    
- 생략