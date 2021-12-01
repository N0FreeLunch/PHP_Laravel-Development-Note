## Permission
- `admin/auth/permissions` PATH에서 설정 가능하다.
- 일반적으로 유저별로 역할을 부여하여 특정 라우터에 접근할 수 있는 권한을 가졌는지를 확인하기 위한 용도로 사용한다.
- 유저에 부여한 역할과 라우터 접근 권한을 매칭하는 방식으로 구성한다.
- 유저와 역할의 관계가 다대다 관계, 역할과 권한의 관계가 다대다 관계로 구성된다.
- 하나의 유저가 여러 역할을 가질 수 있으며, 하나의 역할은 여러 권한으로 접근할 수 있는 관계이다.

### 구성
#### slug
- 권한 계층을 표시하는데 사용한다. dot(.)으로 계층 단어 구분

#### name
- 권한 이름

#### HTTP method
- 허가할 HTTP 메소드를 지정한다.
- 하나의 레코드는 path와 한 짝을 이룬다.

#### HTTP path
- 허가할 HTTP path를 사용한다.
- wildcard를 사용할 수 있다.
- 하나의 레코드는 HTTP method 설정과 한 짝을 이룬다.

### 기능
- 웹 애플리케이션의 각종 PATH에 리퀘스트 처리 허가 거부를 설정할 수 있다.

## Roles
- `admin/auth/roles` PATH에서 설정 가능하다.

### 구성
- slug : 
- name : 역할 이름
- permissions : 여러 권한을 설정할 수 있다. 


## 테이블 구조
### ADMIN 관련 테이블
#### admin_permission
- 접근 대상이 되는 가능한 HTTP 메소드와 URL 세트
#### admin_roles
- 유저에게 부여할 역할을 설정하는 부분
#### admin_role_permissions
- 역할 테이블과 권한 테이블의 다대다 관계를 형성하기 위한 브리지 테이블
#### admin_users
- 역할과 권한을 부여할 대상이 되는 유저를 등록하기 위한 테이블
#### admin_user_permissions
- 
#### admin_munu
- 사이드 메뉴에 메뉴를 등록하기 위한 테이블
#### admin_role_menu
- 사이드 메뉴의 테이블을 특정 역할의 유저에게 보여줄지 말지를 설정하기 위한 테이블

### USER 관련 테이블
#### user
#### admin_user_permissions
#### admin_operation_log
#### admin_role_users

### USER와 ADMIN에 공통으로 속한 테이블
#### user


## 설명
- 어드민의 권한은 role and user, role and menu, role and permission 이 세가지 관계를 통해서 이해하면 된다. 이는 role을 중심에 두고 role이 user, menu, permission과 관계를 이루는 형태이다. 그리고 role을 거치지 않고 user와 permission이 관계를 이룬다.
```
user ----- permission
  \           /
   \         /
    \       /
     \     /
      role
       |
       |
       |
      menu
```
### user와 permission의 관계
- ??
### role과 user의 관계
- 유저에 역할을 부여한다.
### role과 permission의 관계
- 특정 역할에만 특정 HTTP 메소드로 지정된 URL에 접근할 수 있도록 한다.
### role과 menu의 관계
- 특정 역할에만 사이드 메뉴 리스트에 메뉴 버튼을 달아 준다.


