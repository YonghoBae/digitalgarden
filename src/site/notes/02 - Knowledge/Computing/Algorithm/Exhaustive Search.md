---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Algorithm/Exhaustive Search/","tags":["type/study","context/academic","theme/algorithm","status/completed"]}
---

# 완전검색
- 완전 검색 방법은 문제의 해법으로 생각할 수 있는 모든 경우의 수를 나열해보고 확인하는 기법이다.
- Brute-force 혹은 generate-and-test 기법이라고도 부른다.
	- just do it
	- force의 의미는 사람보다는 컴퓨터의 force를 의미한다
- 모든 경우의 수를 테스트한 후, 최종 해법을 도출한다.
- 상대적으로 빠른 시간에 문제 해결(알고리즘 설계)을 할 수 있다.
- 일반적으로 경우의 수가 상대적으로 작을 때 유용하다.
- 모든 경우의 수를 생성하고 테스트하기 때문에 수행속도는 느리지만, 해답을 찾아내지 못할 확률이 작다.
- 우선 완전 검색으로 접근하여 해답을 도출한 후, 성능 개선을 위해 다른 알고리즘을 사용하고 해답을 확인하는 것이 바람직하다.