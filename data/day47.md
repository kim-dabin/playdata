# DAY47

## 학습목표

- 정적 페이지 연동을 구현할 수 있다
- html 문서를 이용하여 브라우저에서 생성된 코드를 이용하여 탐색할 수 있다
- html 엘리먼트와 속성값을 www.w3c.org에서 확인할 수 있다



## 정적 페이지 연동

1. html = head[title]+body
2. body(hn, font, div, br, pre,,,) = .css(tag, id, class)
3. html, 문단태그, 목록태
4. a, img, table, form



> index.html 찾기 또는 검색어 창에 찾을 단어를 입력하게 되면 
>
> index.html -> test01.html -> Res.jsp -> test01.html
>
> ex) 오라클 사이트에서 단어 검색하게 되면 해당 사이트만 보여줌 
>
> 메일 -> Java -> 메일 추출하고 상단 오른쪽에 -> 앞으로 | 목록 | 뒤로 

index.html ->  <**LINK** rel="index" href="index.html">

test01.html ->  <**LINK** rel="Next" href="Res.jsp">

test01.html ->  <**LINK** rel="Prev" href="test01.html">



### a 태그 크롤링 주의점  

1. href가 없을 수있음 
2. rel 주의 
3. a 태그 안에 img가 있을 경우 



### 클라이언트에서 서버에 페이지 요청

- 폼데이터 전송시[submit] get, post 방식이 발생
- [데이터 사이즈  보안]
  - get : 페이지 요청시에 쿼리스트링을 통해서 값 전달
  - post: 폼의 데이터를 이진하로 비곡액 형식으로 젇=ㅡ





```java
while (!flag) {
			try {
				User user;
					user = (User) ois.readObject();
				
					
		

//				System.out.println("붙은 사용자 리스트");
//				for (int i = 0; i < user.getUserList().size(); i++) {
//					System.out.println(user.getUserList().get(i).getNickname());
//				}

//				login.Update(user);

			} catch (ClassNotFoundException | IOException e) {
				e.printStackTrace();
				try {
					oos.close();
					ois.close();
				} catch (IOException ioe) {
					ioe.printStackTrace();
				}
				flag = true;
			} // try~catch end
			Status status = user.getStatus();
			String userNickname = user.getNickname();

			switch (status) {
			case CONNECTED:
				// 현재 접속 유저
				// List<User> nowUserList = user.getUserList();
				System.out.println("WaitingRoomController - login!! ");
				List<User> usertmpList = user.getUserList();

				System.out.println(usertmpList.size());

				userList = new ArrayList<User>();
//				System.out.println(usertmpList.size());
				RoomStatus nowRoomStatus = user.getRoomStatus();

				for (User u : usertmpList) {
					userList.add(u);
				}

				WaitingRoomController.getInstance().setUser(user);
				if (nowRoomStatus == null) {
					user.setRoomStatus(RoomStatus.WAITING);
				}
				// WaitingRoomController.getInstance().ChangeReadyColor(user.getUserList());
				WaitingRoomController.getInstance().changeLabel(user.getUserList());

				break;
			case ROBY:
				System.out.println();

				for (User u : user.getUserList()) {
					// System.out.println("lobby status " + u.getNickname() + " : " +
					// u.getRoomStatus());
					if (u.getNickname().equals(userNickname)) {
						u.setRoomStatus(user.getRoomStatus());
					}

				}
				// System.out.println("lobby "+userNickname+" : "+user.getRoomStatus());

				WaitingRoomController.getInstance().ChangeReadyColor(user.getUserList());
				break;
			case INCORRECT:
				System.out.println("loginController - try again ");
				break;

			case DISCONNECTED:
				userList.remove(user);
				System.out.println("update userList");
				endConnect();
				flag = true;
				break;

			case PLAYING: // game view update
				System.out.println("game playing GameController");
				GameStatus gameStatus = null;
				nickname = LoginController.getInstance().getPlayerName();

				System.out.println("nickname : " + LoginController.getInstance().getPlayerName());
				if (gameStatus == null) {
					// MainApp.switchToGame();
					for (User u : user.getUserList()) {
						// System.out.println("lobby status " + u.getNickname() + " : " +
						// u.getRoomStatus());
						if (u.getNickname().equals(userNickname)) {
							u.setRoomStatus(user.getRoomStatus());
						}
					}
					WaitingRoomController.getInstance().ChangeReadyColor(user.getUserList());
					sendData(user);
				}
				try {
					for (User u : user.getUserList()) {
						// System.out.println("lobby status " + u.getNickname() + " : " +
						// u.getRoomStatus());
						if (u.getNickname().equals(nickname)) {
							gameStatus = u.getGameStatus();
						}
					}

					switch (gameStatus) {
					case HIDE:
						System.out.println("game playing GameController > role - HIDE ");
						break;
					case SEEK:
						System.out.println("game playing GameController > role - seek ");
						break;
					}

				} catch (Exception e) {
					for (User u : user.getUserList()) {
						// System.out.println("lobby status " + u.getNickname() + " : " +
						// u.getRoomStatus());
						if (u.getNickname().equals(userNickname)) {
							u.setRoomStatus(user.getRoomStatus());
						}

					}
					WaitingRoomController.getInstance().ChangeReadyColor(user.getUserList());
					sendData(user);
				}

				break;
			default:
				System.out.println("error");
				break;

			}

		} // while end

		try {
			ois.close();
		} catch (IOException e) {
			System.err.println(" ChatClientThread에의 ObjectOutputStream을 Close하는 중에 IOException이 발생하였습니다.");
		} // catch
```



```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.InetSocketAddress;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Iterator;
import java.util.List;
import java.util.Vector;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Server{
	static ExecutorService executorService; // 스레드풀
	static ServerSocket serverSocket;
	static List<Client> connections = new Vector<Client>();
	
	static void startServer() { // 서버 시작 시 호출
		// 스레드풀 생성
		executorService = Executors.newFixedThreadPool(
			Runtime.getRuntime().availableProcessors()
	    );
		
		// 서버 소켓 생성 및 바인딩
		try {
			serverSocket = new ServerSocket();		
			serverSocket.bind(new InetSocketAddress("localhost", 5001));
		} catch(Exception e) {
			if(!serverSocket.isClosed()) { stopServer(); }
			return;
		}
		
		// 수락 작업 생성
		Runnable runnable = new Runnable() {
			@Override
			public void run() {	
				System.out.println("[서버 시작]");
				while(true) {
					try {
						// 연결 수락
						Socket socket = serverSocket.accept();
						System.out.println("[연결 수락: " + socket.getRemoteSocketAddress()  + ": " + Thread.currentThread().getName() + "]");		
						// 클라이언트 접속 요청 시 객체 하나씩 생성해서 저장
						Client client = new Client(socket);
						connections.add(client);
						System.out.println("[연결 개수: " + connections.size() + "]");
					} catch (Exception e) {
						if(!serverSocket.isClosed()) { stopServer(); }
						break;
					}
				}
			}
		};
		// 스레드풀에서 처리
		executorService.submit(runnable);
	}
	
	static void stopServer() { // 서버 종료 시 호출
		try {
			// 모든 소켓 닫기
			Iterator<Client> iterator = connections.iterator();
			while(iterator.hasNext()) {
				Client client = iterator.next();
				client.socket.close();
				iterator.remove();
			}
			// 서버 소켓 닫기
			if(serverSocket!=null && !serverSocket.isClosed()) { 
				serverSocket.close(); 
			}
			// 스레드풀 종료
			if(executorService!=null && !executorService.isShutdown()) { 
				executorService.shutdown(); 
			}
			System.out.println("[서버 멈춤]");
		} catch (Exception e) { }
	}	
	
	static class Client {
		Socket socket;
		
		Client(Socket socket) {
			this.socket = socket;
			receive();
		}
		
		void receive() {
			// 받기 작업 생성
			Runnable runnable = new Runnable() {
				@Override
				public void run() {
					try {
						while(true) {
							byte[] byteArr = new byte[100];
							InputStream inputStream = socket.getInputStream();
							
							// 데이터 read
							int readByteCount = inputStream.read(byteArr);
							
							// 클라이언트가 정상적으로 Socket의 close()를 호출했을 경우
							if(readByteCount == -1) {  throw new IOException(); }
							
							System.out.println("[요청 처리: " + socket.getRemoteSocketAddress() + ": " + Thread.currentThread().getName() + "]");
							
							// 문자열로 변환
							String data = new String(byteArr, 0, readByteCount, "UTF-8");
							
							// 클라이언트가 stop server라고 보내오면 서버 종료
							if(data.equals("stop server")) 
							{
								stopServer();
							}
							
							// 모든 클라이언트에게 데이터 보냄
							for(Client client : connections) {
								client.send(data); 
							}
						}
					} catch(Exception e) {
						try {
							connections.remove(Client.this);
							System.out.println("[클라이언트 통신 안됨: " + socket.getRemoteSocketAddress() + ": " + Thread.currentThread().getName() + "]");
							socket.close();
						} catch (IOException e2) {}
					}
				}
			};
			// 스레드풀에서 처리
			executorService.submit(runnable);
		}
	
		void send(String data) {
			// 보내기 작업 생성
			Runnable runnable = new Runnable() {
				@Override
				public void run() {
					try {
						// 클라이언트로 데이터 보내기
						byte[] byteArr = data.getBytes("UTF-8");
						OutputStream outputStream = socket.getOutputStream();
						// 데이터 write
						outputStream.write(byteArr);
						outputStream.flush();
					} catch(Exception e) {
						try {
							System.out.println("[클라이언트 통신 안됨: " + socket.getRemoteSocketAddress() + ": " + Thread.currentThread().getName() + "]");
							connections.remove(Client.this);
							socket.close();
						} catch (IOException e2) {}
					}
				}
			};
			// 스레드풀에서 처리
			executorService.submit(runnable);
		}
	}
	
	public static void main(String[] args) {
		startServer();
	}
}
[출처] 자바 클라이언트 서버 모델(4) 스레드풀을 이용한 채팅 서버 구현|작성자 예비개발자


```



```java
public class ServerHandler implements Runnable {
		
		private Socket socket;
		private ClientHandler clientHandler;
		int port;

		ServerHandler(Socket socket) {
			this.socket = socket;
		}

		@Override
		public void run() {
			try {
				System.out.println("server start");
				

				while (true) {
					try {
						Socket socket = serversocket.accept();
						String userip = socket.getInetAddress().toString().replace("/", "");
						System.out.println(socket.getInetAddress() + "가 접속했습니다 : "+Thread.currentThread().getName());
						
						//userList = new ArrayList<User>();
						
						clientHandler = new ClientHandler(userip, socket);
//						clientList.add(clientHandler);
//						Thread clientThread = new Thread(clientHandler);
						//threadList.put(socket.getPort(), clientThread);						
//						clientThread.start();
//						threadPool.submit(clientHandler);
					} catch (Exception e) {
//						if(!serversocket.isClosed())	stopServer();
						e.printStackTrace();
						break;// 끝 
					}//try~catch end 
					
				}//while end
				threadPool.submit(clientHandler);
			} catch (Exception e) {
//				if(!serversocket.isClosed())	stopServer();
				return;// 끝 
			}//try~catch end 

		}// run() end
		
		public void stopServer() { // 서버 종료 시 호출
			try {
				// 모든 소켓 닫기
				Iterator<ClientHandler> iterator = clientList.iterator();
				while(iterator.hasNext()) {
					ClientHandler client = iterator.next();
					client.socket.close();
					iterator.remove();
				}
				// 서버 소켓 닫기
				if(serverSocket!=null && !serverSocket.isClosed()) { 
					serverSocket.close(); 
				}
				// 스레드풀 종료
				if(threadPool!=null && !threadPool.isShutdown()) { 
					threadPool.shutdown(); 
				}
				System.out.println("[서버 멈춤]");
			} catch (Exception e) { }
		}//stopServer() end

	}// ServerHandler end
```

