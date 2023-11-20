# URL, URI

IP 주소는 **어떻게 호스트의 주소를 정하는가?** 를 결정한다면, URL은 더 세분화되어 **호스트 상 자원들에 대한 주소를 어떻게 정하는가?** 를 결정한다.

## URI, URI, URN

- URI : 네트워크 상에 존재하는 자원자체를 나타내는 유일한 고유 식별자이다.

  ```
  scheme:scheme-specific-part:fragment
  ```

    - scheme : data, file, ftp, http, mailto, magnet, news, telnet, ...

  스킴이 생략되면 상대 URI 가 적용이 되고, fragment 가 없으면 순수 URI가 된다.

- URL :  Locator라는 이름 그대로 네트워크상에서 웹 자원의 위치를 의미한다. URI는 어떤 자원인가를 말해주지만 어디서 어떻게 자원을 취하는지는 URL 이 정한다. URI 의 한 형태

  ```text
  protocol(scheme)://user:password@host:port/path;params?query#fragment
  ```



- URN : 영속적이고, 위치에 대해 독립적인 자원에 대한 지시자를 의미한다. 리소를 어떻게 접근할 것인지 명시하지 않고 경로와 리소스 자체를 특정한다.

  ```
  urn:ietf:rfc:2648
  ```

## URL 클래스

클래스 URL은 WorldWieeWeb의 리소스에 대한 위치를 나타낸다. 리소스는 파일이나 디렉터리 처럼 단순한 것일수도 있고, 데이터베이스나 검색엔진에 대한 쿼리와 같은 더 복잡한 개체의 참조일수도 있다.

URL 클래스는 RFC2396에 정의된 이스케이프 메커니즘에 따라 URL 구성 요소를 자체적으로 인코딩하거나 디코딩하지 않는다.

URL에는 URL 이스케이프에 대한 지식이 없기 때문에 동일한 URL의 인코딩 또는 디코딩 형식 간의 동등성을 인식하지 못한다. 예를 들어

```text
http://foo.com/hello world/
http://foo.com/hello%20world
```

서로 동등하지 않은 것으로 간주된다.

URL 클래스는 final class 이므로 상속이 불가하다.

```java
public final class URL implements java.io.Serializable
```

### 생성자

URL 클래스는 4개의 생성자를 제공하며, 자바가 지원하지 않는 프로토콜을 이용하여 URL 객체를 생성할 경우 `MalformedURLException`이 발생한다. 특이한 점은 JDBC와 RMI를 지원하지 않는다. 또한 URL 정보를 저장하고 처리할뿐 유효성검사를 하지 않는다.

```java
public URL(String protocol, String hostname, String file) throws MalformedURLException
public URL(String protocol, String host, int port, String file) throws MalformedURLException
public URL(String spec) throws MalformedURLException
public URL(URL context, String spec) throws MalformedURLException
public URL(URL context, String spec, URLStreamHandler handler)
public URL(String protocol, String host, int port, String file, URLStreamHandler handler) throws MalformedURLException
```

- public URL(String protocol, String hostname, String file) throws MalformedURLException

  프로토콜, 호스트 이름, 파일 이름 3가지 문자열로 URL 객체 생성

  내부적으로 이 생성자는 포트번호를 –1로 세팅함으로써 프로토콜별 default port를 사용하도록 표시한다 (예: HTTP는 80번 포트)

  file은 반드시 슬래쉬( /)로 시작해야 한다

- public URL(URL context, String spec) throws MalformedURLException

  상대 URL과 베이스 URL을 이용하여 절대 URL 객체 생성

  이 생성자는 이미 다른 생성자를 통해 만들어진 베이스 URL에 불완전한 형태의 상대 URL을 보태 절대 URL을 만들어 낸다.

  경로 구성 요소가 슬래시 문자 "/"로 시작하는 경우 경로는 절대 경로로 처리되고 사양 경로는 컨텍스트 경로를 대체한다.

  ```text
  즉 http://metalab.unc.edu/javafaq/javatutorial.html + /boutell/faq/www_faq.html" 은 
  http://metalab.unc.edu/javafaq/boutell/faq/www_faq.html (X)
  http://metalab.unc.edu/boutell/faq/www_faq.html (O)
  ```

### URL 사용하는 메스드

- Java.applet.Applet

  public URL getDocumentBase(), 애플릿을 내포한 HTML 파일이 들어 있던 디렉토리

  public URL CodeBase(), 애플릿이 들어 있던 디렉토리

- java.io.File

  URL toURL()

  주어진 파일에 해당하는 URL 리턴, Windows에서 처리하더라도 file:/D:/JAVA/JNP2/07/ToURLTest.java 형태로 \가 아닌 /가 사용됨에 유의해야한다. 하지만 지금은 depricated

- ClassLoader

  public URL getSystemResource(String name);

  public Enumeration getSystemResources(String name);

  public URL getResource(String name);

- java.swing.JEditpane

  public URL getPage()

- java.net.URLConnection

  public URL getURL()

### 데이터를 읽어오는 메서드

URL 클래스는 생성된 URL 객체로부터 데이터를 읽어 오기 위한 4개 메소드를 제공한다.

```java
public final InputStream openStream() throws IOException
public URLConnection openConnection() throws IOException
public final Object getContent() throws IOException
public final InputStream getContent(Class[] classes) throws IOException
```

- public URLConnection openConnection() throws IOException

  지정된 URL 에 대한 Socket을 열고 URLConnection 객체를 리턴한다. 읽기/쓰기를 모두 가능하도록 I/O 스트림을 얻을 수 있다. URLConnection 은 서버가 보내는 모든 내용을 전달하므로 파일의 내용뿐만 아니라 헤더의 내용까지도 볼 수 있다.

- public final Object getContent() throws IOException

  서버에서 전달하는 MIME 헤더의 Content-type필드를 이용하여 객체 타입 결정된다. 이 메소드 호출로 얻어 지는 객체가 무슨 타입인지 알아 내야 한다

  URL이 지정하는 데이터의 유형에 따라 2가지 유형을 리턴한다.

    - ASCII, HTML 등 텍스트 객체일 경우: InputStream 유형
    - GIF, JPEG 등 이미지 객체일 경우: java.awt.ImageProducer

### Getter

```java
public String getProtocol() 
public String getHost() 
public int getPort() 
public int getDefaultPort() 
public String getFile() 
public String getPath() 
public String getRef() 
public String getQuery() 
public String getUserInfo() 
public String getAuthority() 
```

- public String getProtocol()

  URL 스킴(프로토콜)을 반환

  Sample URL: http://www.hanbit.co.kr

  Return value: http

- public String getHost()

  URL 호스트 이름을 반환

  Sample URL: ftp://anonymous:anonymous@wuarchive.wust.edu/

  Return value: anonymous:anonymous@wuarchive.wust.edu

- public int getPort()

  URL에 지정된 port 번호를 반환, URL에 port번호가 지정되지 않았다면 –1 을 반환

  URL: http://www.userfriendly.com:8080

  Return value: 8080

- public String getFile()

  URL에 지정된 경로와 파일명을 반환

  URL: http://metalab.unc.edu/javafaq/index.html#toc

  Return value: /javafaq/index.html

  파일 경로가 지정되지 않은 URL의 경우 JDK 버전에 따라 다른 값 반환

    - URL: http://metalab.unc.edu

      Jdk 1.0, 1.1, 1.2의 return value: /

      jdk 1.3의 return value: " "(빈 문자열 반환)

- public String getPath()

  getFile()과 동일한 메소드이다.

    - 동일한 메소드가 2개 있는 이유?

      RFC 2396의 URL명세서의 용어와 자바의 용어를 모두 수용하기 위함

      URL의 자원 경로 부분을 URL 명세서에서는 Path, 자바에서는 File 각각 다른 용어를 사용, 두가지 명세서를 모두 수용하기 위한 조치이다.

- public String getQuery()

  URL의 질의 문자열을 반환한다.

  URL: http://metalab.unc.edu/nywc/composition.phtml?category=piano

  Return value: category=piano

- public String getRef()

  URL의Named anchor를 반환한다. Named anchor가 없으면 null 리턴

- public int getUserInfo()

  URL에 사용자 정보와 패스워드 정보를 추가가능 - 스킴과 호스트 사이에 위치 (호스트와 구분을 위하여 "@"를 사용)

  URL: http://mp3:mp3@metalab.unc.edu/

  Return value: mp3:mp3

- public int getAuthority()

  기관(Authority): URL의 스킴(프로토콜)과 경로사이에 위치

  기관(Authority)이 사용자 정보와 포트 정보를 포함할 경우 이 정보까지 포함한 문자열 반환한다.

  URL: http://metalab.unc.edu/

  Return value: metalab.unc.edu

### 동등성

URL 객체가 저장하는 URL 문자열이 동일한가를 비교한다. 동등성 판별을 위해 DNS Resolution을 거친다.

```java
URL www = new URL ("http://www.ibiblio.org/");
URL ibiblio = new URL("http://ibiblio.org/");
System.out.println(www.equals(ibiblio)); // true , DNS에 의해 동일한 문자열 반환
```

`public boolean sameFile(URL other) `메서드는 equals 와 다르게 URL에 FragmentIdentifier는 동등성 검사에 고려하지 않는다.

### 변환

```java
public String toExternalForm() // URI 의 toString()과 동일한 기능
public URI toURI() throws URISyntaxException // URL 은 URI 클래스로 변환
```



## URI 클래스

URI 클래스는 URL 클래스와 다르게 데이터를 가져오는 메서드를 제공하지 않고 **순수하게 자원식별과 URI를 분석하는 기능만 제공**한다. 그러므로 URI 클래스가 URL 클래스 보다 표준을 엄격히 준수한다.

URL 클래스는 네트워크 검색을 위한 애플리케이션 프로토콜이지만, URI는 문자열 분석과 조작만을 위한 클래스이다.

정리하면, URL은 컨텐츠를 다운로드할 목적 , URI 는 검색이 아닌 식별이 목적일때 사용해야 한다.

URI 클래스는 URL 클래스와 달리 프로토콜 핸들러가 필요하지 않는다.  문법적으로만 틀리지 않으면 URI 객체가 표현하는 프로토콜을 자바가 처리할 수 있는가 확인하지 않는다.

```java
public abstract class URLStreamHandler // URL에만 존재
```



URI 클래스는 URL 클래스와 마찬기지로 상속이 불가하다.

```java
public final class URI implements Comparable<URI>, Serializable
```

### 생성자

```java
public URI(String str) throws URISyntaxException
public URI(String scheme, String userInfo, String host, int port, String path, String query, String fragment) throws URISyntaxException
public URI(String scheme,String authority,String path, String query, String fragment) throws URISyntaxException
public URI(String scheme, String host, String path, String fragment) throws URISyntaxException
public URI(String scheme, String ssp, String fragment) throws URISyntaxException 
```

- public URI(String uri) throws URISyntaxException

  이 생성자는 URI 규격과 달리 ASCII 문자 이외에 유니코드 문자도 받아 들인다. 상대 URI(scheme이 null)도 허용되고 ASCII가 아닌 문자도 인코딩이 필요 없다

- public URI(String scheme, String schemeSpecificPart, String Fragment) throws URISyntaxException

  계층형이 아닌 URI에 주로 사용된다 .scheme이 null이면 상대 URI로 적용이 된다. 두 번째 인자인 schemeSpecificPart는 http, mailto 등 scheme마다 다르다

URI 클래스는 생성자 뿐만 아니라 정적 팩터리 메서드도 제공을한다. 이 메서드는 URISyntaxException를 발생시키지 않으며, 이는 문법체크를 하지 않는다는 의미이다.

```java
public static URI URIcreate(String uri) // URI 가 문법적으로 정확하다는 확신이 있을때 사용
```



### URI 의 분류

- 절대/상대 URI

  URI의 scheme 유뮤 상태에 따라 절대/상대 URI 로 구분한다. `public boolean isAbsolute()`로 판별이 가능하다.

- Opaque URI(불분명한 URI)

    - opaque URI는 절대 URI로서 scheme-specific 부분이 슬래쉬('/')로 시작하지 않는 URI이며 파싱이 불가능하다

    - mailto:java-net@java.sun.com

    - news:comp.lang.java

    - urn:isbn:096139210x

    - opaque URI 는 구조가 명확하지 않아서 구성요소를 알아낼때 다음 메서드를 사용한다.

      ```java
      public String getScheme()
      public String getSchemeSpecificPart()
      public String getRawSchemeSpecificPart() //인코딩되어 있으면 인코딩되어있는 그대로
      public String getFragment()
      public String getRawFragment()
      ```



- Hierarchical (구조가 명확한 URI)

    - 계층적(hierarchical) URI는 scheme-specific부분이 슬래쉬로 시작하는 절대 URI이거나 스킴을 가지지 않은 상대 URI 이다.

      ```text
      http://java.sun.com/j2se/1.3/docs/guide/collections/designfaq.html#28
      ../../../demo/jfc/SwingSet2/src/SwingSet2.java
      file:///~/calendar
      ```

      계층적( hierarchical) URI는 다음 문법으로 파싱이 가능하다.

      ```[scheme:][//authority][path][?query][#fragment]```

      authority는 다음 형태

      ```[user-info@]host[:port]```

      Scheme-specific part가 계층적(hierarchical)으로 구성된 URI인가는 `public boolean isOpaque()` 메서드로 알아낼수 있다.

      구조가 명확하므로 URI 구성요소 알아내는 메서드를 사용할 수 있다.

      ```java
      public String getAuthority()
      public String getFragment()
      public String getHost()
      public String getPath()
      public String getPort()
      public String getQuery()
      public String getUserInfo()
      // escape로 인코딩 되어 있으면 디코딩 된 형태로 바꾸어 리턴한다 
      // escape로 인코딩 된 그대로 리턴 받고 싶으면 다음 메소드 사용
      public String getRawAuthority()
      public String getRawFragment()
      public String getRawPath()
      public String getRawQuery()
      public String getRawUserInfo()
      //public String getRawHost()  // (X) 호스명은 오로지 ASCII만 사용
      // public String getRawPort()  // (X) 포트명은 오로지 ASCII
      //생략된 부분에 대해서는 null 리턴
      ```

    