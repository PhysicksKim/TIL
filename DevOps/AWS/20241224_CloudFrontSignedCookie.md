# S3 특정 파일들 접근제한 방법 
## - CloudFront Signed Cookie/URL 사용
  
RSA Key 로 서명된 Cookie/URL 을 가진 사용자만 접근할 수 있도록 제한하면 된다.  
  
### 웹 서버 대상 CloudFront 접근 제한 설정  
 
1. 로컬에서 OpenSSL 등을 이용해 RSA key Set을 발급 (Public/Private)    
2. Public key 를 Cloudfront 에 등록 (CloudFront - 키관리 - 퍼블릭키)    
3. 웹서버에서 AWS SDK 의 CloudFront util 을 이용해 Signed Cookie/URL 발급    
4. 클라이언트는 발급된 Signed Cookie/URL 을 가지고 CloudFront 에서 자원 요청  
5. CloudFront 에서는 쿠키나 URL 쿼리파라미터를 보고 검증 후 인가


### 방법 설명은 생략. 트러블 슈팅 과정 기술  
  
- 3. 웹 서버가 서명된 쿠키 발급
- 4. 클라이언트가 Cookie 를 가지고 검증 인가 요청
  
위 두 과정에서 트러블 슈팅 하느라 이틀을 날림.      
1,2,5 과정은 어려울 거 없음. 
  
> Signed URL 도 제공된 AWS SDK CloudFront Utility 만 있다면 구현 어려울 것 없음 
> 어차피 Java, Javascript 등 메이저 언어는 AWS SDK 가 지원됨    
  
<br>  
<br>

# 트러블 슈팅 사례  

## 1. Cookie 발급 시 Canned Policy / Custom Policy  
  
### 아마도 여러분은 Custom Policy 를 사용해야 할겁니다  
처음 Signed Cookie 를 발급하려 한다면 무작정 "기본 정책으로 간단하게 시작해보자" 라고 생각할 수 있다.    
하지만 Canned Policy 의 문제는 특정 Path 이하 모든 자원을 허용하는 와일드카드 ("/*") 를 사용할 수 없다는 점이다.   
   
![image](https://github.com/user-attachments/assets/2c6de661-d86e-49ad-857d-b12217d92d61)  
> Amazon CloudFront 개발자가이드 - 서명된 쿠키 사용 

표에서 첫 번째 항목을 보면  
"객체에 와일드 카드 문자를 사용해야 합니다" 에서 Canned Policy(미리 준비된 정책) 의 경우 "아니요" 라고 나와있다.  
즉 Canned Policy 는 와일드카드를 사용할 수 없으므로, Custom Policy 를 사용해야 하며   
아마도 쿠키를 발급하는 이유가 특정 Path 이하의 모든 정적 파일 (ex. `myapp/admin/*`) 을 제공하기 위한 목적일테니  
Custom Policy 를 써야한다.  
  
이걸 몰라서 하루동안 삽질함.  
  
<br><br>  

## 2. GPT 믿지말기 
GPT 는 CloudFront Signed Cookie에 대해 잘 모르고, 특히 AWS SDK 에 대해 잘 모른다.  
GPT 는 앞서 말한 Canned Policy 에서 와일드카드가 허용되지 않는다는 점을 모른다.  
  
처음에 Canned Policy 로 구현하고 와일드카드를 넣은 상태에서 자꾸 Access Denied 가 발생하여 하루 종일 고생하는 동안,  
GPT 에게 관련 코드 전체와 관련 AWS Cloudfront 개발자가이드 문서 전체를 던져주고 왜 Access Denied 됐는지 찾아달라고 했지만  
끝까지 GPT 는 "Canned Policy 에서 와일드카드를 사용할 수 없다" 는 제약사항을 지적하지 못했다.   
자꾸 "너가 Policy 서명 잘못한 거 아니야? AWS SDK 대신 내가 직접 문서에 따라 Base64 인코딩 및 RSA 암호화 하는 코드 작성해볼게." 같은 식으로만 나왔다.  
  
그러니까 GPT 믿지말자. GPT는 AWS SDK 잘 모르는 듯 하다.  
  
<br><br>  

## 3. CloudFront Util 이 반환하는 Cookie string 은 {Name}={Value} 형식  
언어에 따라 다를 수 있으나  
String 하나에 쿠키의 이름과 값을 모두 담아놓은 형태로 제공된다.
  
```java
String keyPairIdHeaderStr = cookiesForCustomPolicy.keyPairIdHeaderValue();
System.out.println(keyPairIdHeaderStr);
```
```
CloudFront-Key-Pair-Id=ABCDEFG12345
``` 
  
따라서 `=` 를 기준으로 split 해야한다.  
  
> ### 혹시, 구분글자 말고 쿠키 value 에 = 가 들어가면 어쩌나 생각할 수 있는데
> 애초에 URL 에서 허용하지 않는 값들은 대체문자를 사용한다
> ![image](https://github.com/user-attachments/assets/4161280d-706c-41a8-83c3-c141e6ed8c39)  
  

<br><br>  

## 4. 만약 직접 구현한다면, GPT 로 만들 생각 하지말고 직접 문서를 잘 읽어야 한다.  
```java
private CookiesForCustomPolicy createSignedCookies(String resourceUrl, Instant expirationDate) throws Exception {
  Path privateKeyPath = Paths.get(privateKeyResource.getFile().getPath());
  CustomSignerRequest customPolicyRequest = CustomSignerRequest.builder()
          .keyPairId(keyPairId)
          .privateKey(privateKeyPath)
          .resourceUrl(resourceUrl)
          .expirationDate(expirationDate)
          .build();
  return cloudFrontUtilities.getCookiesForCustomPolicy(customPolicyRequest);
}
```

진짜 어지간하면 그냥 제공해주는 AWS SDK 에 있는 `CloudFrontUtilities` 를 쓰자.  
구현 과정에서 알고리즘 적으로 어려운건 전혀 없는데  
디테일 챙기려면 문서를 꼼꼼히 읽어야 한다  
  
예를 들면  
- base 64 인코딩 후 URL에 허용되지 않는 문자는 대체문자로 변환해야 한다    
- policy json 구조 양식  
- SHA1 해시로 변환  
등등 여러 디테일을 챙겨야 해서 실수하기 마련이다.  
특히 대충 AWS 문서 던지고 GPT 한테 만들라고 맡기면  
무조건 GPT가 구현 실수내서 하루종일 Access Denied 만 볼거다.    
  
심지어 이게 제대로 서명됐는지 알기 위해서는 직접 쿠키값을 수동으로 넣고 CloudFront 에 던져보는 수 밖에 없는데다가
에러 로그도 그냥 Access Denied 밖에 건질 수 없기 때문에
직접 구현하기 여간 불편한 게 아니다.  
   
그리고 애초에 `CloudFrontUtilities` 가 다 제공해주니까  
GPT 답변 따라서 직접 막 json 구성하고 삽질할바에  
인터넷에 예제 코드 검색해서 따라하면 그냥 해결된다.  
  
> javascript 는 `const { getSignedCookies } = require("@aws-sdk/cloudfront-signer");` 라고 해서 쓰면 된다.  
> 각 언어별 sdk 는 알아서 찾도록.
  
<br><br>  

## 5. html 태그에다가 `crossorigin="use-credential"` 설정       
  
[MDN HTML attribute: crossorigin](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin)  

요청에 쿠키를 포함해서 보낼려면 `use-credential` 옵션이 필요하다.   
이는 정적자원 요청 뿐만 아니라 axios 등 HTTP 클라이언트를 사용해봤다면 알고 있는 설정일 것이다.  
  
마찬가지로 CloudFront Signed Cookie 도 `use-credential` 이 필요할텐데  
이를 위해서 html 에서 정적 자원을 필요하는 태그에 `crossorigin="use-credential"` 이라는 옵션을 달아줘야 한다.  
  
webpack 을 쓴다면 [output.crossOriginLoading 옵션](https://webpack.js.org/configuration/output/#outputcrossoriginloading) 을 참조하거나  
(GPT 답변으로 얻은) 아래와 같은 플러그인을 직접 구성하는 식으로 해결할 수 있다.    
  
```
plugins: [
  {
    apply: (compiler) => {
      compiler.hooks.compilation.tap('AddCrossoriginPlugin', (compilation) => {
        HtmlWebpackPlugin.getHooks(compilation).alterAssetTagGroups.tapAsync(
          'AddCrossoriginPlugin',
          (data, cb) => {
            // 스크립트와 링크 태그에 crossorigin="use-credentials" 추가
            [...data.headTags, ...data.bodyTags].forEach((tag) => {
              if (tag.tagName === 'script' || tag.tagName === 'link') {
                tag.attributes.crossorigin = 'use-credentials';
              }
            });
            cb(null, data);
          },
        );
      });
    },
  },
]
```

<br><br> 

## 6. CloudFront CORS 문제 - use-credential 시 Allow-Origin 와일드카드 * 불가  
  
내 앱의 경우 `https://gyechunsik.site` 에서 쿠키를 발급해주고   
`https://static.gyechunsik.com` 으로 정적파일을 요청해야 하기 때문에 CrossOrigin 상태이다.   
  
따라서 CORS allow 헤더들이 제공되어야 하는데  
S3 의 CORS 헤더 정책에서 Allow Origin 을 * 로 설정해 뒀었다.    
  
이게 권한이 필요 없는 파일이라면 * 가 문제 없으나  
`use-credential (쿠키전송)` 옵션을 사용하려면 allow origin이 * 가 아니라 명시되어 있어야만 한다.    
다시 말하면 `Access-Control-Allow-Origin: *` 이면 쿠키를 보낼 수 없다.   
  
> [해당 MDN 문서 설명](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/Errors/CORSNotSupportingCredentials)
  

이를 위해서 S3 전체 CORS 정책을 바꾸기에는 부담스러우므로  
CloudFront 의 동작(behavior)를 변경해서 특정 자원들에 대해서만 CORS 헤더를 덮어 씌울 수 있다.  
  
<br>  
  
![image](https://github.com/user-attachments/assets/15a814c1-139a-4bcb-b784-1e30fda7fff5)  
  
엑세스 제한 설정을 하는 CloudFront 동작(behavior)에 들어가서, 위 이미지에 보이는 `응답 헤더 정책 - 선택 사항` 을 만들어 주면 된다.  
  
![image](https://github.com/user-attachments/assets/c64e4b82-5ef1-4bf9-b6b9-da816db4559c)  
![image](https://github.com/user-attachments/assets/c9468fda-be12-495f-8e00-6107c4adf7df)
응답 헤더 정책 설정은 위와 같이 해주면 되고  
특히 아래에 별표친 `Access-Control-Allow-Credentials` `오리진 재정의` 는 둘 다 체크해줘야 한다.  
  
- Access-Control-Allow-Credentials : credential(쿠키) 허용할거임?
- 오리진 재정의 : S3 에서 제공한 헤더가 있어도(오리진) 덮어씌울거임?(재정의)    
  
### 특히 `"오리진 재정의"` 중요    
오리진 재정의 안해주면 계속해서 S3 에서 제공하는 형태로 헤더가 제공되니까 꼭 유의하자.  
