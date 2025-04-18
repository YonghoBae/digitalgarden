---
dg-publish: true
---

### **소프트웨어 공학에서의 Inspection: 전체 강의 내용 정리**

---

#### **Inspection이란?**

- **Inspection**은 소프트웨어 개발 과정에서 산출물을 단계별로 검토하여 오류를 찾아내는 체계적인 활동입니다.
- Testing과 달리 소스 코드를 실행하지 않고, 오류가 발생하는 시점과 가장 가까운 위치에서 문제를 발견할 수 있습니다.
- 이는 **오류 발견 가능성을 높이고**, **오류 수정 과정**을 단순화하여 개발 효율성을 증대시키는 데 중요한 역할을 합니다.

---

#### **Inspection의 주요 특징**

1. **Testing과의 차이점**:
    
    - Testing은 소스 코드를 실행하고 데이터 입력을 통해 오류를 확인하지만, Inspection은 코드 실행 없이 오류를 검토합니다.
    - Testing은 오류의 원인을 파악하고 수정하는 과정이 복잡할 수 있지만, Inspection은 각 단계에서 오류를 검출하므로 효율적입니다.
2. **효율성**:
    
    - Inspection은 프로젝트 초기 단계부터 오류를 검출하므로 수정 비용을 절감하고 품질을 높이는 데 기여합니다.
3. **산출물의 단계별 검토**:
    
    - 소프트웨어 개발 생명주기(SDLC)의 각 단계별로 수행되어, 이후 단계에 미칠 영향을 최소화합니다.

---

#### **Inspection의 필요성**

- **초기 오류 발견**: 오류를 조기에 발견함으로써 비용 절감.
- **문제 근원 파악**: 문제 발생 원인을 보다 명확히 식별.
- **품질 향상**: 반복적인 검토 과정을 통해 개발품의 전반적인 품질 향상.

---

#### **Inspection 절차**

1. **프리팩(Pre-planning)**
    
    - Inspection 준비 단계로, 검토할 산출물과 그 목적, 범위를 정의합니다.
    - 관련 문서를 준비하고, 참여할 인스펙터를 선정합니다.
2. **개요 단계(Overview Phase)**
    
    - 개요 단계는 **선택적 단계**로, 경우에 따라 생략할 수 있습니다.
    - 이 단계는 다음과 같은 경우에 주로 수행됩니다:
        - 새로운 기술(AI, 머신러닝 등)이 프로젝트에 도입된 경우.
        - 기존 방식과 다른 시스템을 검토하는 경우.
        - Inspection이 조직 내 처음 도입되는 경우.
    - **내용**:
        - 검토 대상 산출물의 목적, 이후 단계와의 관계 설명.
        - 프로젝트의 전반적인 흐름과 앞으로의 계획 공유.
3. **준비 단계(Preparation Phase)**
    
    - Inspection 프로세스에서 가장 중요한 단계로, **개별적인 검토 활동**이 이루어지는 과정입니다.
    - 각 인스펙터는 주어진 문서를 검토하고 오류를 찾아냅니다.
    - **활동**:
        - 이전 단계의 산출물이나 관련 문서를 참조하여 산출물의 목적과 내용을 이해합니다.
        - **체크리스트**를 활용하여 오류의 유형을 체계적으로 기록합니다.
        - **Preparation Log** 작성:
            - 발견된 결함.
            - 검토에 소요된 시간.
        - 작성된 로그는 **모더레이터(Moderator)**에게 제출됩니다.
4. **Inspection 미팅(Inspection Meeting)**
    
    - 각 인스펙터의 로그를 종합하여 오류 목록을 정리하고 논의하는 과정입니다.
    - **활동**:
        - 모더레이터가 오류의 심각도를 평가.
        - 오류의 유형을 분류.
        - 오류의 수정 여부 및 방법 논의.
    - **오픈 이슈(Open Issue)**:
        - 해결되지 않은 오류는 추후 단계에서 처리합니다.
        - 예: 전문적인 검토가 필요한 경우, 외부 전문가의 의견을 요청.
5. **서드아워(Side Hour)**
    
    - 해결되지 않은 문제를 논의하고, 최종적으로 문제를 해결하는 단계입니다.
    - **참여자**:
        - 기존 인스펙터.
        - 외부 전문가(필요 시).
        - 프로젝트 매니저.
    - **활동**:
        - 남아 있는 문제를 분석하고, 추가적인 작업 계획 수립.
6. **수정 및 검증(Rework and Follow-up)**
    
    - **발견된 오류 수정**:
        - 심각한 오류(Major Defect)는 반드시 수정.
        - 단순한 오류(Minor Defect)는 비용 효율성을 고려하여 수정 여부 결정.
    - **부차적 오류 점검**:
        - 수정 작업 중 발생할 수 있는 **Secondary Defect** 확인.
7. **Inspection 평가(Inspection Evaluation)**
    
    - Inspection 프로세스와 결과를 평가하는 단계입니다.
    - **평가 기준**:
        - 발견된 오류 수 및 유형.
        - 인스펙터의 활동 평가.
        - 프로세스의 효율성 평가.
    - **목적**:
        - 향후 개선 방안을 도출하고, 프로세스의 품질을 높이는 데 활용.

---

#### **Inspection의 결과물**

1. **결함 유형 분류**:
    
    - 명확성 결여(Clarity).
    - 정확성 문제(Accuracy).
    - 일관성 부족(Consistency).
    - 기타 결함 유형.
2. **Preparation Log**:
    
    - 검토 과정에서 발견된 오류 목록과 검토 시간 기록.
3. **오류 리스트**:
    
    - 최종 확정된 오류 목록과 심각도 정보.

---

#### **강의에서 강조한 주요 사항**

1. **준비 단계의 중요성**:
    
    - 오류를 발견하는 첫 단계로, 철저한 검토 필요.
    - 각 인스펙터는 산출물의 도메인 지식과 관련 문서를 참조하여 검토해야 함.
2. **체크리스트 활용**:
    
    - 오류를 체계적으로 기록하고, 중복 오류를 방지하기 위한 도구.
3. **오픈 이슈 처리**:
    
    - 심각한 오류는 반드시 해결하며, 해결되지 않은 문제는 Side Hour에서 논의.
4. **프로세스 평가**:
    
    - 발견된 오류의 수뿐 아니라, 프로세스 자체의 효율성과 효과성도 평가.
5. **결과 저장 및 활용**:
    
    - 발견된 결함과 그 처리 과정을 데이터베이스에 저장하여 향후 프로젝트에 활용.

---

강의에서 다룬 모든 내용을 왜곡 없이 한국어로 변환하였습니다. 추가 설명이나 수정이 필요하면 말씀해주세요!