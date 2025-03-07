# Base64
악성코드 Base64
(블로그)[https://itstudycube.tistory.com/41]

## 일단은 ==로 끝나면 base64 decode해보자
왜냐
Base64 도 내겐 알아두면 항상 까먹고 쓸 때 헷갈리는 것 중에 하나이다.
Base64 인코딩 디코딩은 보통 http 통신으로 바이너리 데이터나 url을 주고받을 때 등 주로 사용된다.
Base64는
RFC 4686에 따르면 Base64 인코딩은 임의의 바이트들을 대소문자 영문 등으로 이루어진 형태로 나타낼 수 있도록 고안되었다. US-ASCII에서 사용하는 65 글자가 사용되었으며 6비트당 한 글자를 표현한다. a-z, A-Z, 0-9 , +, / 로 64글자이며, 65번째 = 는 패딩용이다(= 는 아래 추가 서술).
 
3 바이트 = 24비트 = Base64인코딩된 4 글자
인코딩하려는 바이너리를 왼쪽부터 3바이트(24 비트) 단위로 끊어서 인코딩하는 데, 6비트당 Base64 Table에 따라 한 글자씩 매핑되어, 결론적으로 4 글자로 나온다.
아래는 Base64 Table

 패딩용 =
만약 인코딩 대상이 24비트보다 작다면 남은 비트 자릿수를 0으로 채워 6비트의 형태가 되도록 만들고 = 로 매핑한다. 이때 아래와 같은 케이스들이 있는 데;, 
- 8 비트일 때, 16비트를 0으로 채우며, 끝 12비트를 == 로 표현한다.- 16 비트일 때, 8비트를 0으로 채우며, 끝 6비트를 =  로 표현한다.- 24 비트일 때, = 를 사용하지 않는다.

이 패딩용 = 때문에 의심해볼만 suspicious Attempt 라고 판단.
```java
package testBase64;

import java.nio.charset.StandardCharsets;
import java.util.Base64;


public class Main {
    public static void main(String[] args) {
        System.out.println("============== Start Base 64 Encoding & Decoding ! ==================");

        System.out.print("Target String is ");
        String aString = "abc";

        System.out.print("Byte length of abc is ");
        System.out.println(aString.length());

        byte[] aStringBytes = aString.getBytes(StandardCharsets.UTF_8);

        StringBuilder sb = new StringBuilder();

        for (byte aStringByte : aStringBytes) {
            sb.append(String.format("%8s", Integer.toBinaryString(aStringByte))
                            .replaceAll(" ", "0"));
        }
        System.out.println("abc in Binary is ");
        System.out.println(sb);

        System.out.print("binary length of abc is ");
        System.out.println(sb.length());

        Base64.Encoder encoder = Base64.getEncoder();
        String base64EncodedAString = encoder.encodeToString(aStringBytes);
        byte[] base64EncodedAStringBytes = encoder.encode(aStringBytes);

        System.out.print("Base64 Encoded abc is ");
        System.out.println(base64EncodedAString);

        System.out.print("Base64 Encoded abc length is ");
        System.out.println(base64EncodedAString.length());
        
        Base64.Decoder decoder = Base64.getDecoder();
        
        byte[] decodedAStringBytes = decoder.decode(base64EncodedAStringBytes);

        StringBuilder sb2 = new StringBuilder();

        for (byte aStringByte : decodedAStringBytes) {
            sb2.append(String.format("%8s", Integer.toBinaryString(aStringByte))
                            .replaceAll(" ", "0"));
        }

        System.out.println("Base64 Decoded abc in Binary is ");
        System.out.println(sb2);

        String decodedAString = new String(decodedAStringBytes, StandardCharsets.UTF_8);
        System.out.print("Base64 Decoded abc is ");
        System.out.println(decodedAString);

        System.out.println("============== End Base 64 Encoding & Decoding ! ==================");
    }
}
```
