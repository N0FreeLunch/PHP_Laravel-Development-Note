## 개요

## 메뉴 설정
- `/admin/auth/menu` PATH로 접속하여 메뉴를 추가할 수 있다.
- 설정된 메뉴를 표시하기 위해서는 새로고침 버튼을 통해 리로드 해야한다.


## 메뉴 설정 항목
### parent
- 메뉴를 메뉴 구성의 트리 계층의 어디에 위치시킬지를 설정할 수 있다. 

### title
- 메뉴의 이름을 설정한다.

### icon
- fontawesome에서 제공하는 아이콘 키워드를 입력하여 메뉴의 아이콘을 설정할 수 있다. http://fontawesome.io/icons/

### URI
- 메뉴에 링크를 걸어준다. 
- 링크 될 페이지가 `도메인/admin/demo/users`이면 `/demo/users` 또는 `demo/users`를 적어 주면 된다.
- 외부 링크의 경우에는 전체 URL을 적어준다.

### roles
- ??

### permission
- `/admin/auth/permissions`에서 권한 설정을 할 수 있다.
- 권한 이름을 설정할 수 있으며, 권한 이름에 따라 접근을 허가할 PATH와 HTTP 메소드 세트를 지정할 수 있다.
- 권한이란 PATH에 따른 CURD의 제한을 만드는 것이다.
- menu에 permission을 설정하는 것은 ??
