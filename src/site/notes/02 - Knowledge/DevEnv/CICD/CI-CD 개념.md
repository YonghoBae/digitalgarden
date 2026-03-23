---
{"dg-publish":true,"permalink":"/02 - Knowledge/DevEnv/CICD/CI-CD 개념/","tags":["type/study","context/studies","theme/devenv","status/completed"]}
---

# CI/CD

> 코드 변경을 자동으로 빌드·테스트·배포하여 릴리즈 주기를 단축하는 개발 파이프라인

## CI (Continuous Integration, 지속적 통합)

- 개발자가 코드를 변경할 때마다 **자동 빌드 + 자동 테스트** 실행
- 목적: 코드 통합 시 발생하는 충돌·버그를 조기에 발견
- 결과물: 검증된 아티팩트(JAR, Docker 이미지 등)

## CD (지속적 제공 / 배포)

| 구분 | Continuous Delivery (지속적 제공) | Continuous Deployment (지속적 배포) |
|------|----------------------------------|-------------------------------------|
| 배포 승인 | **수동** (담당자 승인 후 배포) | **완전 자동** (승인 없이 자동 배포) |
| 특징 | 언제든 배포 가능한 상태 유지 | 테스트 통과 시 즉시 프로덕션 반영 |

## 배포 파이프라인 흐름

```
코드 Push → CI 빌드/테스트 → 아티팩트 생성 → (스테이징 배포) → [승인] → 프로덕션 배포
```

## 주요 도구

| 도구 | 특징 |
|------|------|
| [[02 - Knowledge/DevEnv/CICD/Github Actions/Github Actions\|GitHub Actions]] | GitHub 저장소와 통합, YAML 워크플로우 |
| [[02 - Knowledge/DevEnv/CICD/Jenkins/Jenkins\|Jenkins]] | 오픈소스, 플러그인 생태계 풍부, 자체 서버 필요 |
| GitLab CI | GitLab 내장, Runner 기반 |

## 면접 포인트

- **CI/CD의 목적**: 배포 주기 단축, 빠른 피드백, 인적 오류 감소
- **CI와 CD의 차이**: CI는 코드 품질 검증, CD는 검증된 코드의 배포 자동화
- **Continuous Delivery vs Deployment**: 수동 승인 유무가 차이점
- **도구 선택 기준**: GitHub Actions(간편 통합), Jenkins(커스터마이징)

## 관련 개념

- [[02 - Knowledge/DevEnv/CICD/Github Actions/Github Actions\|GitHub Actions]]
- [[02 - Knowledge/DevEnv/CICD/Jenkins/Jenkins\|Jenkins]]
