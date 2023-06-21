> 공부하면서 쓰이는 API 명령어를 복사해서 쓰기위해 작성한 글

### FileInputStream

```java
FileInputStream fis = new FileInputStream("파일위치");
			Scanner scan = new Scanner(fis);
```

### FileOutputStream

```java
FileOutputStream fos = new FileOutputStream("파일위치");
			PrintWriter pw = new PrintWriter(fos, true, Charset.forName("UTF-8"));
```
