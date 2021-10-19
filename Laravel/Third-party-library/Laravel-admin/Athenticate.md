# 인증

## 현재 세션의 유저
### 어드민 유저 객체 가져오기
```
Admin::user();
```

### 어드민 유저 아이디 가져오기
```
Admin::user()->id;
```

### 어드민 유저의 역할 가져오기
```
Admin::user()->roles;
```
- 라라벨 미들웨어의 role과 유사할 것으로 보인다.

### 어드민 유저의 권한 가져오기
```
Admin::user()->permissions;
```

### 어드민 유저의 역할 확인
```
Admin::user()->isRole('developer');
```
- 어드민 유저의 권한 중에 'developer'라는 권한이 있는지 확인한다.

### 유저에게 권한을 부여
```
Admin::user()->can('create-post');
```

### 어드민 유저의 권한을 제거
```
Admin::user()->cannot('delete-post');
```

### 현재 세션의 어드민 유저가 관리자 권한을 가지고 있는지 확인
```
Admin::user()->isAdministrator();
```

### 주어진 역할에 속하는 역할을 가지고 있는지 확인
```
Admin::user()->inRoles(['editor', 'developer']);
```
