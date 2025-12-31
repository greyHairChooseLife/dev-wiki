


[github issue](github_infra_issuehttps://github.com/Namanmoo-Damso/infra/issues/10)
[github Pull_Request](https://github.com/Namanmoo-Damso/infra/pull/11)

- CI의 한 단계로써 AI 리뷰를 만들어봤다.
    - PR 올릴 때마다 AI가 리뷰 남기기
    - 리뷰 시간을 상당히 단축시킬 수 있고, 효용도 높일 수 있을것으로 기대된다.

- Pull Request에 코멘트만 남기는 것도 write 권한이 필요한게 특이했다.

- aws와 github 사이에 길을 뚫어줘야했다. 
    - action이 실행될 때마다 aws resource에 접근해야하기 때문에 좀 더 까다롭게 굴었다.
    - aws IAM role을 추가하고, github을 aws의 Web Identity Provider로 추가해줘야했다.
        - 깃헙 정도는 그냥 선택지로 넣어줬으면 좋겠다.
    - 이후 s3 버킷을 하나 만들고, 이에 접근하는 권한도 뚫어줬다. AI가 남긴 리뷰와 PR의 맵핑 정보를
      마크다운 파일로 모두 저장해서 모든 팀원들이 각자 학습 등 용도로 가질 수 있게 해주고싶었다.
        - 나도 갖고 싶었다...

- 크래프톤 정글에서 지원해준 크레딧으로 aws bedrock을 사용했다. 감사합니다.

- github action은 브랜치별로 반영되는지 어떤지 헷갈리는 부분들이 좀 있었는데, 이 과정에서 많이 명쾌해졌다.


