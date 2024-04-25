---
hide:
title: 'domain status: clientHold'
date: 2024-03-26
---

## 문제 인식

2주 쯤 전에 구입하여 서비스를 연결시켜둔 도메인이 갑자기 접속이 안된다.

그런데 nginx를 통해 동일한 컨테이너를 연결해둔 다른 도메인으로는 접속이 잘만 된다.

## 문제 원인

도메인이 ICANN에 의해 정지되었다. AWS 계정을 통해 처음 도메인을 구입하면, AWS 가입시 했던 인증과는 별개로
도메인 소유자 인증을 해야한다.

이걸 안해도 일정기간 정상 이용 가능한데, 약 3~15일 정도다. 이것이 지나면 도메인이 정지(suspended)된다.

## 문제 해결

1.  도메인 구입처를 통해 소유자 인증을 해야한다. 내 경우는 AWS Route53를 통해 구입했으니, AWS Route53
    콘솔에서 인증을 진행했다. (이메일 다시보내기를 위한 배너가 떴다.)

2.  도메인 소유자 정보가 인증되었음을 알리는 메일이 왔다.

    > Dear AWS customer,
    >
    > Your email address [인증 받은 이메일] has been successfully verified for domain reg istration. No
    > further action is required on your part.
    >
    > Regards, Amazon Route 53

3.  _(이후 호스트존을 재생성해도 도메인 정상화가 안된다.)_

4.  _(검색하다보니 AWS support에 티켓을 발행하여 해결했다는 후기를 봤다. 나도 남긴다.)_

5.  _(AWS support의 가이드라인에 따라 1)_ 보니까
    [whois 웹사이트](https://whois.gandi.net/en/results?search=example.com)에서 확인한 NS와 호스트존에 설정된
    NS가 달랐다. 엉뚱한 집에 가서 주문한거 달라하니 줄 수가 있나...

6.  _(AWS support의 가이드라인에 따라 2)_ **domain에 할당된 name server를 재설정했다. 호스트존이 아니라
    도메인에 설정된 NS를 변경하는 것이었다.**

        <details>
        <summary>가이드 펼치기</summary>
         Hello,

    Thank you for following up.

    I understand that the domain is not currently available on internet. I will guide you further.

    Upon detailed review, I see that the name servers in the hosted zone and the public whois look up do not
    match hence the domain is not resolving.

    The current hosted zone for your domain have the following NS records:

    new-ns1.myns new-ns2.myns new-ns3.myns new-ns4.myns

    However a WHOIS lookup for the same domain lists different Name Server

    - https://whois.gandi.net/en/results?search=example.com

    old-ns1.myns old-ns2.myns old-ns3.myns old-ns4.myns

    When these values don't match, your website or web application can be intermittently unavailable on the
    internet. To make these values match, perform the following procedure:

    1. Navigate to the “Hosted zones” page for your domain at your Route 53 console
       (https://console.aws.amazon.com/route53/home )
    2. Click on “Hosted zones” at the left column
    3. Click on the \*\*\*round-radio button next to the hosted zone name associated with your domain.
    4. At the far right column, make a note (click to copy) the four name servers from this newly created
       hosted zone:

       new-ns1.myns new-ns2.myns new-ns3.myns new-ns4.myns

    5. Navigate to the **_“Registered domains” details page for your domain at your Route 53 console
       (_**Click “Registered domains” at the left column)
    6. Click on the domain name in question
    7. Click **_“Add or edit name servers” (_**highlighted in blue, at the far right of the page)
    8. Update the name servers to match the four name servers from the newly created hosted zone:

       new-ns1.myns new-ns2.myns new-ns3.myns new-ns4.myns

    9. Monitor the operation via a WHOIS lookup
    10. Allow up to 48 hours for the change to propagate
    11. Test to make sure your domain is resolving correctly per the record sets you have setup.

    You can find more information to how to update the name servers for your domain at the following link:
    https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-name-servers-glue-records.html

    I trust you find this helpful, but please don't hesitate to request more information, or ask additional
    questions, we’ll be here to reply as quickly as possible.

    We value your feedback. Please share your experience by rating this and other correspondences in the AWS
    Support Center. You can rate a correspondence by selecting the stars in the top right corner of the
    correspondence.

        </details>

## 되짚기

생각해보면 host zone과 record를 생성하는것은 DNS가 내 도메인을 어떻게 다룰지 설정할 뿐이다. 도메인에 접속시
어떤 NS로 연결되는지는 별개다.

host zone을 생성할 때 NS를 자동으로 4개 할당해준다. 이것때문에 전혀 의심하지 않은 부분이었다. 만약 ICANN에서
직접 도메인을 구입했고, AWS Route53은 DNS로만 사용했다면 이런식으로 헷갈릴 일이 없었을 것이다.

또, DNS를 항상 Domain Name Server로 생각했는데, D-N-System으로 정확히 이해하는게 좋겠다. 이 시스템이
운영되는 서버니까 DNS로 이해해도 그간 문제가 없었는데, 개념을 얼렁뚱땅 편하게 이해해버리는게 이번에 큰
비효율을 낳았다.

### 문제 해결을 위한 나의 여정

<details>
<summary>펼치기</summary>

1. DNS 관련 문제라고 생각했다. 일단 브라우저에서도 그렇게 말하고, 서비스가 죽은것이 아니라는것을
   확인했으니까.

   > 서비스 성숙도 면에서도, 시기적으로도 사용이 아주 활발한 서비스는 아니었으나 어쨌든 실사용으로
   > 개시되었기 때문에 나는 마음이 급했다. 주말이라 다행이었지만, 정확한 원인 분석과 공부보단 빠른 해결이
   > 중요했다.

2. AWS route53에 등록된 레코드를 다시 한번 살펴봤다. 레코드를 재생성하거나 호스트존을 삭제하고 다시
   생성해보기도 했다. 안된다.
3. 혹시 nginx에서 upstream으로 설정해둔 컨테이너를 여러 도메인에 연결시켜서 문제가 된걸까? 근거는 없지만
   간단한 테스트이니 nginx config에서 단일한 server로 설정해봤다. 그래도 안된다.
4. 컨테이너로 제공중인 서비스와 관련된 문제인지 파악하기 위해, 도메인에 upstream 서비스 말고 그냥 text를
   제공하도록 설정해봤다.

   ```nginx
   server {
     listen 80;
     server_name mydomain.problem;

     location / {
       return 200 'Hello, world!';
     }
   }
   ```

   안된다.

5. 그럼 이제 도메인이 서버의 ip로 잘 연결되는지를 확인할 차례다. AWS route53을 DNS로 사용하고 있으니,
   route53에서 확인해보자. 친절하게도 _레코드 테스트_ 기능을를 제공해준다. 잘 연결이 되어있다. DNS도
   문제없다.

6. 그런데 생각해보니 도메인을 구입할 수 있는 곳은 AWS 뿐만 아니다. 이 말은 도메인을 관리하는 기구(?)는
   별도로 존재할 수 있다는 것이다.

   내 도메인 리스트에 들어가본다. 도메인 구입 계약 상태나 소유주에 대한 정보와 함께 뭔가 있을 수 있다.
   그랬더니 ![대문짝만하게 배너](../imgs/"domain status: clientHold".png)가 뜬다. 메일을 인증 해 달라고...

7. 좀더 검색해보니 정해진 기간(약 15일) 안에 도메인 소유주 인증을 완료하지 않으면(도메인 소유자로 등록한
   주소에 이메일이 왔었다.) ICANN에 의해 suspended 된다고 한다.

</details>
