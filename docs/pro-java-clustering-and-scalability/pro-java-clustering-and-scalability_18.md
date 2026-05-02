# 15. 登录

让我们开始了解一些 Spring Security 配置。

```
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
@Autowired
private UserDetailsService userDetailsService;
@Override
protected void configure(HttpSecurity http) throws Exception {
http
.csrf().disable()
.formLogin()
.loginProcessingUrl("/login")
.loginPage("/")
.defaultSuccessUrl("/chat")
.and()
.logout()
.logoutSuccessUrl("/")
.and()
.authorizeRequests()
.antMatchers("/login", "/new-account", "/").permitAll()
.antMatchers(HttpMethod.POST,  "/chatroom").hasRole("ADMIN")
.anyRequest().authenticated();
}
@Autowired
public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
auth
.userDetailsService(userDetailsService)
.passwordEncoder(bCryptPasswordEncoder());
}
@Bean
public BCryptPasswordEncoder bCryptPasswordEncoder() {
return new BCryptPasswordEncoder();
}
}
```

在 `configure` 方法中，所有操作都通过 `HttpSecurity` 对象完成，该对象提供了一个流畅的接口。¹

*   身份验证将通过登录 Web 表单完成。
*   当表单向 `/login` 提交 POST 请求时，Spring Security 将为你处理身份验证。
*   登录页面是根上下文 `/`。
*   登录成功后，用户将被重定向到 `/chat` URI。
*   注销 URI 是 `/logout`，注销操作后，用户将被重定向到 `/` URI，即登录页面。
*   URI `/login`、`/new-account` 和 `/` 允许所有人（包括匿名用户）访问。
*   只有具有 `ROLE_ADMIN` 角色的用户才允许向 `/chatroom` 发送 POST 请求（以创建聊天室）。
*   任何其他请求仅允许已登录用户访问。

当表单登录提交到 `POST /login` 时，Spring Security 将拦截该请求，查询 MySQL 实例以获取用户，并检查提供的凭据是否正确。但是，如果你不指示 Spring 如何执行此操作，事情将无法进行。这并非那么神奇！这就是为什么你需要实现 `UserDetailsService` Spring 接口来指示 Spring 如何执行此操作。幸运的是，正如你在第 8 章的“Spring Data JPA 仓库”部分所学到的，你正在使用 `UserRepository`，它已经提供了一种针对 MySQL 实例执行 `User` 操作的简便方法。

```
@Service
public class UserDetailsServiceImpl implements UserDetailsService {
@Autowired
private UserRepository userRepository;
@Override
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
User user = userRepository.findOne(username);
if (user == null) {
throw new UsernameNotFoundException("User not found");
} else {
Set grantedAuthorities = user.getRoles().stream().map(role -> new SimpleGrantedAuthority(role.getName())).collect(Collectors.toSet());
return new org.springframework.security.core.userdetails.User(user.getUsername(), user.getPassword(), grantedAuthorities);
}
}
}
```

在前面的代码片段中，`UserRepository` 用于查询数据库以查找给定的 `username`。请注意，在配置类中，`UserDetailsService` 的一个实例被提供给了 `AuthenticationManagerBuilder`。

`BCryptPasswordEncoder` 组件用于使用 bcrypt² 加密用户的密码。

![A453568_1_En_15_Figa_HTML.jpg](img/A453568_1_En_15_Figa_HTML.jpg) 这里，我们禁用了跨站请求伪造（CSRF）保护³ 以简化事情。如果你想启用它，只需移除 `.csrf().disable()` 这一行，并在 HTML 表单中添加 `<input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}"/>`，以便将 CSRF 令牌与表单数据一起发送。此外，如果你启用它，`/logout` 将需要是一个 POST 请求。

脚注 1

[`https://www.martinfowler.com/bliki/FluentInterface.html`](https://www.martinfowler.com/bliki/FluentInterface.html)

2

[`https://en.wikipedia.org/wiki/Bcrypt`](https://en.wikipedia.org/wiki/Bcrypt)

3

[`https://docs.spring.io/spring-security/site/docs/current/reference/html/csrf.html`](https://docs.spring.io/spring-security/site/docs/current/reference/html/csrf.html)



