# String 관련 메소드
## toCharArray()
- `String 배열 -> char 배열`로 만들어준다.
- 예시
  - 프로그래머스 lv0 -[수 조작하기 1](https://school.programmers.co.kr/learn/courses/30/lessons/181926)
  ``` java
    class Solution {
    public int solution(int n, String control) {
        int answer = n;
        for(char ch : control.toCharArray()){
            switch(ch){
                case 'w' : answer += 1; break;
                case 's' : answer -= 1; break;
                case 'd' : answer += 10; break;
                case 'a' : answer -= 10; break;
                    default:break;
            }
        }
        return answer;
    }
  ```