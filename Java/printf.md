# printf()
- println()의 단점
    - 실수의 자리수 조절불가 - 소수점 n자리까지 출력 못함
    - 10진수로만 출력 - 다른 8,16진수로 출력 불가
- printf()로 출력형식 지정 가능.

```
		System.out.printf("%d%n",15); //10진수 %d
		System.out.printf("%#o%n",15); //8진수 %o 접두사: #붙임
		System.out.printf("%#x%n",15); //16진수 %x 접두사: #붙임
		System.out.printf("%s%n", Integer.toBinaryString(15)); //2진수
		
		// float보다 double이 더 정확함.
		float f = 123.456789f;
		System.out.printf("%f%n", f);

		double f1 = 123.456789;
		System.out.printf("%f%n", f1); //실수형식 %f
		System.out.printf("%e%n", f1); //지수형식 %e
		System.out.printf("%g%n", f1); //간략한 형식 %g
		
		System.out.printf("[%5d]%n", 10); // 5자리 출력
		System.out.printf("[%-5d]%n", 10); //5자리출력 왼쪽정렬
		System.out.printf("[%05d]%n", 10); //빈공간 0으로 출력
		
	
		double d = 1.23456789;
		System.out.printf("%14.6f%n", d); //14=공간수, 6=소수점자리
		System.out.printf("%14.10f%n", d); //왼쪽공간=빈공간, 오른쪽 =0으로 채
		
		System.out.printf("[%s]%n", "www.naver.com"); 
		System.out.printf("[%20s]%n", "www.naver.com"); //20 = 자리수
		System.out.printf("[%-20s]%n", "www.naver.com"); //-20 = 왼쪽정렬
		System.out.printf("[%.9s]%n", "www.naver.com"); // .9 = 출력할 자리수.
```