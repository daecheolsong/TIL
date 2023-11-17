# InetAddress

InetAddress 클래스는 자바에서 IP 주소를 표현하는 클래스이다.

자바에서는 모든 IP 주소를 InetAddress 클래스를 사용한다.

## 생성자

InetAddress 클래스에는 **public 생성자** 가 없다.  InetAddress 클래스의 기본생성자는 default 이기 때문에 new 연산자를 이용하여 새로운 인스턴스를 생성할수 없다.

```java
InetAddress() {
        holder = new InetAddressHolder();
}
```

InetAddress 클래스를 생성하고 싶다면,  5개의 static 메서드를 이용하여 생성할수 있다.

```java
public static InetAddress getByName(String host) throws UnknownHostException
public static InetAddress getByAddress(String host, byte[] addr) throws UnknownHostException
public static InetAddress[] getAllByName(String host) throws UnknownHostException
public static InetAddress getLocalHost() throws UnknownHostException
public static InetAddress getByAddress(byte[] addr)
```

- getByName (String host)

  이 메서드는 실제로 네트워크를 통해 로컬 DNS 서버와 연결되어 호스트명에 해당하는 숫자 주소를 가져온다.

  이에 따라 DNS 서버가 주소를 찾지 못하면 `UnknownHostException`이 발생한다.

  host 가 dotted IP 로 주어질때,이 IP 에 대응되는 호스트 이름이 찾아지지 않을경우 호스트 이름은 처음 주어진 dooted IP가 되고 `UnknownHostException`도 발생하지 않는다.

- getAllByName(String host)

  이 메서드는 호스트 이름에 대응하는 모든 주소를 리턴한다.  즉 하나의 도메인 이름에 여러개의 IP 주소를 가진 경우에 사용한다.

  호스트를 찾을수 없을 경우 `UnknownHostException`이 발생한다.

- getLocalHost()

  이 메서드는 현재 이 메서드가 실행되고 있는 호스트에 대한 InetAddrss 객체를 리턴한다.

  네트워크가 연결되지 않고 IP 주소가 없을 경우에 Loopback 주소를 반환한다. (Host 명 : localhost, IP : 127.0.0.1)

  - **InetAddress.getLocalHost() vs InetAddress.getByName("localhost")**

    ```text
    user-pc/XXX.XXX.XXX.XXX vs localhost/127.0.0.1
    ```

- getByAddress(byte[] addr), getByAddress(String host, byte[] addr)

  두 메서드의 차이는 호스트명을 InetAddress가 호스트명의 유무이다. 숫자로 IP 주소를 가지고 있으면 DNS에 의존하지 않고 IndetAddress를 생성할수 있다.

  또한 이 두 메서드를 이용하여 존재하지않거나 검색되지 않은 호스트에 대한 InetAddress 객체를 생성할 수 있다.  이는 호스트가 존재하는가에 대한 보장을 하지 않는다. 단순히 바이트 배열이 4바이트인지, 16바이트 길이인가 검증하여 아니면 `UnknownHostException`이 발생한다.

> **IP 주소로 검색하기**
>
> IP address 문자열을 인자로 getByName(String host) 를 호출한 경우 DNS 참졸를 생략하고 InetAddress 를 생성한다.  이는 실재로 호스트가 존재하는지 여부를 알수 없다.  DNS lookup을 하려면, `getHostName()` 을 명시적으로 호출하면 된다.  그러나 이 상황에서 호스트를 찾을수 없다해도 **UnknownHostException 은 발생하지 않는다.**  결론은 IP address 문자열 대신 호스트명을 사용하는것이 안정적이다.

## Getter

```java
public String getHostName() // 호스트명을 모르고 있을때한 DNS 호출
public String getCanonicalHostName() // getHostName() 보다 적극적으로 DNS 호출, FQDN 반환
public String getHostAddress() 
public byte[] getAddress()// IP 주소를 바이트 배열로 리턴 , 처리시 Oxff 변환해야 한다.
```



## IP 주소의 특별한 의미를 검증하는 메서드

```java
public boolean isAnyLocalAddress()
public boolean isLoopbackAddress()
public boolean isLinkLocalAddress()
public boolean isSiteLocalAddress()
public boolean isMulticastAddress() // 멀티캐스트용 주소
public boolean isMCGlobal() // 전세계 대상 멀티캐스트용 주소
public boolean isMCNodeLocal() // 네트워크 인터페이스내 멀티캐스트용 주소
public boolean isMCLinkLocal() // 서브넷 내 멀티캐스트용 주소
public boolean isMCSiteLocal() // 사이트 내 멀티캐스트용 주소
public boolean isMCOrgLocal() // 조직내 멀티캐스트용 주소
```

- public boolean isAnyLocalAddress()

  와일드 카드(Wildcard) 주소이면 true 리턴

  와일드 카드 주소는 서버나 게이트웨이가 여러 개의 네트워크 인터페이스를 가진 경우의 대표 주소로 쓰여 로컬 시스템의 어느 주소와도 매치됨.

  0.0.0.0(IPv4) 또는 0:0:0:0:0:0:0:0 (a.k.a. ::) (IPv6)

- public boolean isLoopbackAddress()

  루프백(Loopback) 주소 여부 판별한다. 루프백 주소는 소프트웨어 테스트용

  네트워크에 실제로 접속하지 않고 네트워크 프로그램 테스트 가능하다. 루프백주소로 ping하면 IP소프트웨어가 제대로 동작하고 있는지 확인 가능하고  클라이언트에서 동일 기계의 서버에 메시지를 보내는 테스트를 하는것이다.

  루프백 주소로 접속하는 것과 그 시스템의 IP 주소로 접속하는 것은 다름 127.0.0.1(IPv4) 또는 0:0:0:0:0:0:0:1 (a.k.a. ::1) (IPv6)

- public boolean isLinkLocalAddress()

  IPv6 링크 로컬 주소 여부 판별한다. 링크 로컬 주소의 패킷은 라우터가 로컬 서브넷 너머로 보내지 않는다.

  링크 로컬 주소는 FE80:0000. 0000: 0000 + 8자리의 Local 주소 또는 Ethernet MAC 주소이다.



## 패킷 도달가능성 검사하기

현재 노드에서 특정 노드로 패킷이 전송가능한지 테스트 해 볼수 있다.

```java
public boolean isReachable(int timeout) throws IOException
public boolean isReachable(NetworkInterface interface, int ttl, int timeout) throws IOException
// ttl: Time-To-Live 패킷이 이동하는 최대한의 홉(Hop)수
```



## NetworkInterface 클래스

호스트가 가진 네트워크 인터페이스를 표현하는 클래스로 이름과 이 인터페이스에 할당된 로컬 IP 주소들로 이루어진다.

NetworkInterface 객체로부터 로컬 주소를 얻고, 로컬 IP address -> InetAddress -> Socket, ServerSocket 순으로 처리된다.

### 팩토리 메소드

InetAddress 클래스처럼 특정 네트워크 인터페이스의 NetworkInterface 객체를 리턴하는 정적 팩토리 메소드가 존재한다

- public static NetworkInterface getByName(String name) throws SocketException

  특정 이름을 가진 네트워크 인터페이스의 NetworkInterface 객체를 리턴한다.

  - UNIX: eth0, eth1, lo

  - Windows: CE31, ELX100

- public static NetworkInterface getByInetAddress(InetAddress address) throws SocketException
  - 특정 IP 주소를 가진 네트워크 인터페이스의 NetworkInterface 객체를 리턴한다.

- public static Enumeration getNetworkInterfaces() throws SocketException

  로컬 호스트의 모든 네트워크 인터페이스의 NetworkInterface 객체 목록을 리턴한다.

### Getter

- public Enumeration getInetAddresses()

  하나의 네트워크 인터페이스에 둘 이상의 IP 주소가 할당되었을 때 유용하다.

- public String getName()

  eth0, lo 같은 특정 네트워크 인터페이스 이름을 리턴한다.

- public String getDisplayName()

  "Ethernet card 0" 와 같은 사람이 읽기 쉬운 모양의 네트워크 인터페이스 이름을 리턴한다.