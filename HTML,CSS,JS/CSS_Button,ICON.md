# 버튼 만들기

### 텍스트의 길이에 따라 버튼의 크기가 비례하게 변한다.

- figma를 참고하여 크기 설정
- 버튼 이름에 명사를 넣는게아니다.
    - main 버튼 등의 이름을 사용.
    
    ```css
     <button class="btn-main">버튼</button>
    ```
    
- 버튼 만들때 고려해야할 사항
    
    ```css
    .btn-main{
        background-color: var(--color-main-3);
        padding: 8px 20px;
        border: none;
        border-radius: var(--radius-3);
        color:var(--color-base-0);
        font-weight: bold;
    }
    ```

### 반응형 버튼 만들기

- 아이콘을 만들때부터 **반응형**을 고려해서 만들어야한다.
- media를 이용하여 반응형 버튼을 만든다.
    
    ```css
    @media (min-width: 700px) {
        .btn-main{
            border-radius: 0;
        }
    }
    ```
    
- **바뀌어야할 게 있고 바뀌지 않아야할것이 있다면?**
    - md를 이용한다.
    
    ```css
    @media (min-width: 700px) {
        .md\:btn-main{
            border-radius: 0;
        }
    }
    ```
    

### btn의 크기는 같지만 폰트의 수와 색상이 다를경우

- btn-reverse 클래스를 이용한다.

### btn의 비활성화상태

- btn-disable 클래스를 이용한다.