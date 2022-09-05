# merge commit revert 후 다시 merge

오늘(9월 5일) 한 동료가 B 브랜치를 A 브랜치에 merge를 했는데 코드가 반영되지 않는다고 도움을 요청했다.

확인해보니 이전에 B 브랜치를 A 브랜치에 merge한 후 해당 merge commit을 revert한 commit이 있었다.

이 revert commit이 의심돼 revert commit을 다시 revert 하고 merge를 진행하니 문제가 해결됐다.

이런 결과를 보면서 다음과 같은 사실을 다시 상기할 수 있었다.

1.  커밋는 변화량을 담고 있다.
2.  merge commit revert는 merge 자체를 제거하는 것이 아니라 merge로 발생된 변화를 없애는 변화는 적용하는 것이다.
    실험으로 다음과 같은 결과도 확인 할 수 있었다.
    1. B브랜치를 A브랜치에 merge (m commit)
    2. (m commit) 을 revert
    3. A 브랜치르를 B브랜치에 merge

       => 결과 B 브랜치에 B 브랜치에서 작업한 내용이 삭제됨
