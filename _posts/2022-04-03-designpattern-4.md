---
layout: post
title:  " [Java] Adapter 패턴"
date:   2022-04-03
desc: "spring security에서 볼 수 있는 Adapter패턴"
keywords: "DesignPattern"
categories: [Designpattern]
tags: [designpattern]
icon: icon-html
---

## Adapter 패턴

### 기존 코드를 클라이언트가 사용하는 인터페이스의 구현체로 바꿔주는 패턴

+ 클라이언트가 사용하는 인터페이스를 따르지 않는 기존 코드를 재사용할 수 있게 해준다.

![어댑터-1](https://user-images.githubusercontent.com/37110261/161409645-5f016ebe-9b38-4eff-ad13-000559c5c231.PNG)


<br/>

### 기존의 코드를 인터페이스를 따르도록 하여 재사용할 수 있도록 하는 예시로 spring security가 있다.

+ Oauth2.0을 구현할때 구글,네이버,카카오등 구현 방식이 조금씩 다를 수 있고 로그인에서 사용자(Account)를 불러오고, 활용하는 데이터가 다를 수 있다. 이때 어댑터 패턴을 사용하여 공통화된 인터페이스로 감싸서 사용하는 예시 코드를 구현해보자

<br/>

### 상황: 사용자는 Account 도메인과, 이에대한 로직을 갖고 있는 AccountService를 이미 사용중이고 코드변경은 불가하다. 하지만 spring security를 사용하여 다른 구글,네이버등의 로그인 서비스를 이용하고 싶은 상황이며 이를 위해 공통화된 기능 구현이 필요하다.

+ client에서 아래처럼 사용하고 싶은 상황

``` java
public class App {
    public static void main(String[] args) {
        AccountService accountService=new AccountService();

        UserDetailsService userDetailsService=new AccountUserDetailsService(accountService);

        LoginHandler loginHandler=new LoginHandler(userDetailsService);

        String login=loginHandler.login("홍길동","홍길동");
        System.out.println(login);
    }
}
```

<br/>

+ 구현할 interface

``` java
public interface UserDetailsService {
    UserDetails loadUser(String name);
}
```

``` java
public interface UserDetails {
    String getUsername();
    String getPassword();
}
```

<br/>

+ Account : 기존에 사용하고 있던 유저 도메인 코드

``` java
public class Account {
    private String name;

    private String password;

    private String email;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

<br/>

``` java
public class AccountService {

    public Account findAccountByUsername(String username){
        Account account=new Account();
        account.setName(username);
        account.setPassword(username);
        account.setEmail(username);
        return account;
    }
}
```

<br/>

+ 기존의 코드를 건드리지 않고 인터페이스를 구현하고 확장하기 위해 해당 인터페이스를 구현하는 코드를 작성한다.

``` java
public class AccountUserDetailsService implements UserDetailsService {
    private AccountService accountService;

    public AccountUserDetailsService(AccountService accountService) {
        this.accountService = accountService;
    }

    @Override
    public UserDetails loadUser(String name) {
        Account account=accountService.findAccountByUsername(name);
        return new AccountUserDetails(account);// UserDetails 를 구현하는 AccountUserDetails로 account를 주입하여 반환
    }
}
```

``` java
public class AccountUserDetails implements UserDetails{
    private Account account;

    public AccountUserDetails(Account account) {
        this.account = account;
    }

    @Override
    public String getPassword() {
        return account.getPassword();
    }

    @Override
    public String getUsername() {
        return account.getName();
    }
}
```
<br/>

+ UserDetailsService를 통해 로그인 서비스를 동작시킨다.

``` java
public class LoginHandler {
    UserDetailsService userDetailsService;

    public LoginHandler(UserDetailsService userDetailsService) {
        this.userDetailsService = userDetailsService;
    }

    public String login(String username,String password){
        UserDetails userDetails=userDetailsService.loadUser(username);
        if(userDetails.getPassword().equals(password)){
            return userDetails.getUsername();
        }else{
            throw new IllegalArgumentException();
        }
    }
}
```

### 최종적으로 LoginHandler를 통해 해당 UserDetailsService를 구현한 구현체를 의존성 주입을 받으면서 기존의 코드를 수정하지 않고 클라이언트가 사용하는 인터페이스 구현체를 사용하게 만들었다.

<br/>

## 어댑터 패턴의 장단점
### 장점
+ 기존 코드를 변경하지 않고 원하는 인터페이스 구현체를 만들어 재사용할 수 있다.
+ 기존 코드가 하던 일과 특정 인터페이스 구현체로 변환하는 작업을 각기 다른 클래스로 분리하여 관리할 수 있다.

### 단점
+ 새 클래스가 생겨 복잡도가 증가할 수 있다. 경우에 따라서는 기존 코드가 해당 인터페이스를 구현하도록 수정하는 것이 좋은 선택이 될 수도 있다.
