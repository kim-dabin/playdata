# Day46

## 학습목표

- 빅데이터에 대한 동향
- HTML



## 알고리즘 관점의 동향

- 데이터 <-> 알고리즘 <-> 플랫폼 

- 텐서플로우 
- 빅데이터 분석은 사업의 수단 -> 빅데이터 분석은 사업 그 자체 





## 활용관점의 동향

- 빅데이터 분석 활용 방향이 어떻게 변화하고 있는 지 파악함 
- 빅데이터를 분석해서 어떻게 보여주는 가 
  - 일반차트 
  - 통계전문 차트
  - 시각화 차트(그래픽)
  - 리포트 -> 대시보드 
- 빅데이터 분석으로 어떤 비즈니스를 하는 가 
  - 기업에서의 비즈니스 변화
    - 데이터 + 알고리즘
  - 비즈니스 체계의 변화
    - 시장경제 VS 계획경제 by 마윈
- 빅데이터를 어떻게 효율적으로 관리해서 보여주는 가
  - 추천시스템



## 실제 기업에서 원하는 분석

- 어떤 분석이 필요할 지 기업 입장에서 생각함 
- 통계분석 vs 단순한 리포트 
- 데이터 마이닝 vs 시각화
- 온라인 분석



## 개념적인 분석 기법 리뷰

- 고객의 가치 
  - 과거가치 
  - 현재가치
  - 잠재가치



## 데이터 분석 방법의 구분

### 데이터 분석 방법들을 구분 지어서 특징을 파악함

- 데이터 의미 표현을 위한 분석
  - OLAP
  - Reporting
  - 시각화
- 추측과 의미 파악을 위한 분석
  - 통계 
  - 마이닝
  - 기계학습
- 의사결정에 직접 활용하기 위한 분석
  - 최적화
  - 예측(최근 가장 선호하는 비즈니스)
  - 시뮬레이션



## 대시보드

- 종합적인 정보 조회 가능

- 이종 데이터 소스로부터 구성

- d3js

- 대시보드 분석

  - 필터링 분석 
  - 공통 필터, 마스터 필터, 범위 필터 
  - 추적분석(Tracking Analysis), 스토리(Story) 분석 

  

## 시각화

- 미시 시각화
  - 탐색 중심 
  - 빅데이터의 세부 부분을 여러 관점에서 분석하기 위해 다양한 차트를 조합해 연계하여 탐색
- 거시 시각화
  - 직관적인 표현 중심
  - 빅데이터의 전체적인 의미르르 직관적으로 인지시키기 위해 적합한 유혀이 그래픽을 이용



## 통계



```html
정치가 
  세종은 조선의 제4대 왕이다. 성은 이, 휘는 도, 본관은 전주, 자는 원정이다.
  사후 묘호는 세종, 시호는 장헌대왕으로,, 정식 시호는 [세종장헌영문예무인성명효대왕]이다.

가족관계
1397년 음력 4월 10일(양력 5월 15일) 당시 정안군이던 태종과 민제의 딸 원경왕후 민씨의 육남으로 태어났다. 위로는 형 양녕대군 이제, 효령대군 이보 및 세명의 요절한 친형이 있었고, 정순공주, 경정공주, 경안공주 등 동복 친 누나 세 명이 있었다. 아명은 막동이고, 후에 이름을 도(祹)로 개명하였다. 140ㅁ8년 12살에 충녕군(忠寧君)에 봉해졌다. 어려서부터 독서와 공부를 좋아하였으며, 두 형과 함께 빈객으로 임명된 계성군 이래(李來)와 변계량에게 수학하였다. 그 후에는 정몽주의 문하생인 성리학자 권우의 문하에서 수학하였다. 어려서부터 책을 한시도 손에 놓지 않아 눈병과 과로로 건강을 해치기도 하여, 부왕 태종은 책을 모두 감추게 하기도 했다. 그러나 그는 부왕이 숨겨둔 책들을 찾아내어 독서를 계속하였다.
```





```java
import java.io.IOException;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Date;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class SimpleThreadPoolSocketServer {
	// 연결할 포트를 지정합니다.
	private static final int PORT = 8080;
	// 스레드 풀의 최대 스레드 개수를 지정합니다.
	private static final int THREAD_CNT = 5;
	private static ExecutorService threadPool = Executors.newFixedThreadPool(THREAD_CNT);
	public static void main(String[] args) {

		try {
			// 서버소켓 생성
			ServerSocket serverSocket = new ServerSocket(PORT);

			// 소켓서버가 종료될때까지 무한루프
			while(true){
				// 소켓 접속 요청이 올때까지 대기합니다.
				Socket socket = serverSocket.accept();
				try{
					// 요청이 오면 스레드 풀의 스레드로 소켓을 넣어줍니다.
					// 이후는 스레드 내에서 처리합니다.
					threadPool.execute(new ConnectionWrap(socket));
				}catch(Exception e){
					e.printStackTrace();
				}
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}

// 소켓 처리용 래퍼 클래스입니다.
class ConnectionWrap implements Runnable{

	private Socket socket = null;

	public ConnectionWrap(Socket socket) {
		this.socket = socket;
	}

	@Override
	public void run() {

		try {
			// 응답을 위해 스트림을 얻어옵니다.
			OutputStream stream = socket.getOutputStream();
			// 그리고 현재 날짜를 출력해줍니다.
			stream.write(new Date().toString().getBytes());

		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				socket.close(); // 반드시 종료합니다.
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
}
```

