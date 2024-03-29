# Security:
  - out of the box:
      - content negoatiation
      - login / logout page
      - user
      - protection aganist common attacks [csrf , cors ...
      - cache control
          - Cache-Control: no-cache, no-store, max-age=0, must-revalidate
          - add this header to respones from authenticated endpoint. we can disable it or add our owm. 
        - to prevent caching the content of secured content. when someone use back in the browser after 
            the user logged out the content will not be loaded because it's not cached.
        - in controller u can in endpoint that return non sensitive data, u can enable it when 
          returning a ResponseEntity .cacheControl(CacheControl.maxAge(30, TimeUnit.SECONDS))    
    - X-Content-Type-Option: nosniff
        - tell the browser not to guess the Content-Type because certain browsers ignore 
            the Content-Type header and try to guess it.      
        why? in certain situation u can upload a polyblack  file(image|gif) and it can be a valid JS as well,
        then u can execute a cross-site scriptting attack by uploading an image.
        - other consideration - if user will upload a file u don't let him choose the filename because 
            it might impact the content-type , we don't want to serve the file from the same domain because if
            we serve it from a separate domain, then we're enabling same origin policy to protect us.

    - X-Frame-Options: DENY    
        Disabling Framing -> prevent user from clicking on our site without being aware of it[Clickjacking].
        - done by overlaying a malicious site on the top of the target site, the malicious site can load the target
          site in the background and any action on the malicious site are taking place on the target site.
        --config-
        u can allow frame from the same origin -> #HttpSecurity.headers().frameOptions().sameOrigin()
        or for a specific domain  #HttpSecurity.headers().addHeaderWriter(new StaticHeadersWriter("X-FRAME-OPTIONS", "ALLOW_FROM domain.com"))
        - disallow ur app to be rendered in iframe
        
    - X-XSS-Protection: 1; mode=block
      Reflective XSS
      prevent browser from rendering a page if it suspects a Reflective XSS attack.
        Reflective XSS attack is when content is submitted or injected[malicious code] into ur web app and executed
          by the client browser against our app.
        
      1 - stored
        - hacker can execute malicious script in ur app, can steal ur cookies, sessions
        - how ? u recieve an input fron the user and u didn't encode it well. -> hacker can execute any js code.
      2 - Reflicted
        - parse request param and hacker provide a script in it. chrome protect aganist this by default.
      3 - Down base attack[]
      4 - mutation XSS , content of page that returned from the server is modified by the browser before it is rendered in the browser.

      how to prevent XSS Attack? in server side we make sure we provide context sensitive encoding - 
        in client side u make sure u use safe js api.
    - CSRF [only use the CSRF token for state changing ops such as POST, PUT, DELETE, PATCH]
        - this attack is taking advantages of that a browser will send any cookies associated with 
        a certain domain to that domain  .
            - we have session cookie for bank.com, when another website[attacker.com] send a req from
            ur browser to bank.com the browser will send the cookie [session associated with bank.com]
            with the request.
        - spring use [Synchronizer Token Pattern]     to protect against CSRF, spring send 
          a randomly generated token with cookies to the browser [browser has no knowledge about it because it an app specific]
          - so spring will expect from client to send the token with each state changing request or the request will be rejected
            by CsrfFilter
        - in thymleaf form <input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}" />

        - if u using SPA, u can configure csrf().csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse()) to place token inside the cookie ,
          that way the jsClient can extract the token from the cookie and send it with the request. 
          the CsrfFilter will compare the token in the request against the one in the cookie.
          <b> Browser restrict JS to only access the cookies for their domain. </b>
          - don't store it in the cookie
        - if u use stateless basic or digest authentication u still need CSRF protection as the Browser
          will include the basic and digest Authorization header by default
      - csrf token [synchronized token pattern]. every request  that change the state of ur application 
          needs to have a CSRF token in it. and then spring security will validate that - if it's invalid -> forbidden
          - CsrfFilter -> a token repository is in charge of loading the default csrf token, that token is, 
            by default stored into the HTTP session, u can customize it to be placed in a cookie which is not quit as secure
            but it make sense in SPA. which the JS looks for the csrf token to be included automatically in a cookie.
      - if u use thymleaf _csrf token will be included by default. if not the csrf token is included in the request
        attributes as a attribute name as _csrf.
      - one special case is multi-part upload or file upload. data is not transferred all at once,
        instead it's transferred in segments, for that reason we may not be able to read the csrf token<
          TO get around this is to include the token in the action instead of as a parameter for the request .
          alternatively u can include Spring's multipart request filter ahead of spring security but this means a malicious
            user could upload files before we able to validate the csrf token. it's better to include it in the action.
      <form mthod="post" encrypt="multipart/form-data" th:action="@{/upload(_csrf=${_csrf.token})}">
    - SameSite:
      * if u disable csrf and add spring-session dependency it will be lax.
      - default with spring security: its value is empty
      - strict: JSESSIONID will not be sent with any cross-site request(3rd party request).
      - lax: JSESSIONID will be sent when a request is from 3rd party site but it's coming from the same application or top level redirect, or a GET request.

  - CSP
    to access contentSecurityPolicy HttpSecurity.headers.contentSecurityPolicy("value")        
  - Https 
      - to make spring redirect any request for HTTP to HTTPS .requiresChannel()
          .requestMatchers(r -> [match requests in production for example. 
          because development with http is easier])  hsts headers will included by default, u can disable or configure it.
  - getPrinciple -> access to currently logged in user.
  - ..
  - FilterChainProxy -> composing other filters into a single filter.
      - entry point of all web based security.
  - spring scurity focus on application security
      - Authentication | Authorization | protection aganist common attack.
      - separation between authentication and authorization because of authentication is one time thing. 
          authorization spread through the entire application.
  
  - antMatchers vs mvcMatchers:
      - use antMatchers when u use spring security without spring [strut for example]
      - when u use spring use mvcMatchers /foo -> willmatch -> /foo.json /foo.php ..
      - keep order in ur mind most restrictive endpoints in the top.
      .access('spEl')
      .access("@beanName.predicateMethod(param1, param..)")
      .mvcMatchers("/users/{name}").access("#name == principal?.username") <- the user only can access his data only.
      - map (RequestMatcher, Collection<ConfigAttribute>) FilterInvocationSecurityMetadataSource --> 
          SecurityMetadataSource[default impl -> ExpressionBasedFilterInvocationSecurityMetadataSource]. 
      - AccessDecisionManager[route of all things access in spring security] 3 impl 
          [default: AffirmativeBased (1 yes -> access), ConsensusBased (majority yes -> access), UnanimousBased (all say yes)]
      - PasswordEncoderFactories.createDelegatingPasswordEncoder
          - new DelegatingPasswordEncoder(id, encoders[id -> endcoder])
          - when u want to migrate password to use another encoder , implement UserDetailsPasswordService and override 
          updatePassword and sprind will call it automatically.
      - WebExpressionVoter -> AccessDecisionVoter<FilterInvocation>
          FilterInvocation:
              - curr req, curr response, context[all configAttributes]
      - all these work after [AbstractSecurityInterceptor 2 impls (1: FilterSecurityInterceptor, 2: MethodSecurityInterceptor)]
      FilterWriter
      
      
      - Oauth is a framework that allows people to grant limited access to protected resources on the web. 
          the term access implies that oauth is about authorization, and this is true. however, 
              open Id connect [OIDC], formalizes a way to authenticate with oauth as well.
  - CommonOAuth2Provider -> contains info about well-known oauth2 providers.
  - ClientRegistrationRepository[InMemoryClientRegistrationRepository] ->
  - to initiate login flow OAuth2AuthorizationRequestRedirectFilter [if resolve the req then] redirect to OAuth endpoint itself.
  - to get the authorization code and exchange it for a valid user -> [OAuth2LoginAuthenticationFilter] -> 
      pass it to AuthenticationManager that delegat it to [OAuth2LoginAuthenticationProvider] that get 
          a response using the access token response client. 
  - from accessToken we need to get the user information for login using OAuth2UserService
      --> in this case we're just accessing a URI with the access token that was provided. And then create a user from that.
      - if u were using JWT, we could create it from id token itself.

- Authentication#getName()
    - formLogin
    - return username field
    - openid
    - subject
    - Non-openid
    - vary depending on the custom impl[]FB/geithub claim -> id]
    ------
    FilterChainProxy composing other filters into a single filter
----

- 1-req from the client hit servlet container.
- 2-servlet container look up filter in web.xml
- 3-DelegatingFilterProxy[spring-security-filters registered with web container] delegate to FilterChainProxy which contains SecurityFilterChain
- 4-filter registered in web.xml with filter-mapping[filter-name, url-pattern].

  - securityFilterChainProxy loop over FilterChain to send req to matched filterChain.

- 5-FilterChain
  - ChannelProccessingFilter
  - SecurityContextPersistentFilter -> manage securityContext[hold details of authenticated principal] 
    - if there is no authenticated principal in securityContext repo[httpSessionRepo in web], it 'll add an empty securityContext to securityContextHolder[thread local].
  - AuthenticationFilters   -> if authentication succeeded it will set Authentication  in the securityContext.
  - RememberMeAuthenticationFilter
  - AnonymousAuthenticationFilter -> set AnonymousAuthenticationObject in securityContext.
  - ExceptionTranslationFilter -> handle exceptions thrown by FilterSecurityInterceptor. may redirect client to an error page. or update headers.
    - contains authenticationEntryPoint#defaultEntryPoint#LoginUrlAuthenticationEntryPoint -> generate login page.
  - FilterSecurityInterceptor -> check authorization against authenticationPrincipal.

- 6- when request hit AuthenticationFilters:
  - 1- filter will extract credientals from req and create approperiate AuthenticationToken
  - 2- send it to authenticationManager which iterate over AuthenticationProvider to find which supports that token and authenticate it against that provider. 
  - 3- provider use UserDetailsService to load data from where we store the identities.
    - return authenticated authenticationPrincipal if the info is valid and set the principal to the SecurityContext.
- Header[WWW-Authenticate: Basic realm="Realm"] tell the browser basicAuth was required.       

-------
  - adding spring security to the app , u effectivly adding a single filter [[delegating filter proxy]]
    - this filter passing a request to a collection called a FilterChainProxy -each FilterChain -> handle different request path
    - adding oauth2Login -adds-> oauth2AuthorizationRequestRedirectFilter and oauth2LoginAuthenticationFilter[handle the exchanges of the authorization code]
       all this all customizable: - when use sign a request to oauth2 =request redirected to RedirectEndpoint which redirect the user to Authorization server. 
       which redirect to the authorization endpoint with the authorization code. authorization endpoint call token endpoint with auth-code, client-id|secret, state.
       - when u register new user from oauth2/oidc u must u use a unique immutable claim from the token 
          to make sure that is never change. in oidc sub is unique. don't use for example the email if the
            auth-server allow users to change their email. <- check the doc if the auth server don't support oidc.
            onAuthenticationSuccess(req, response, Authentication) -> the impl of Authentication is depending on how the
              user authenticate so u need to check its type[UsernamePasswordAuthenticationToken, OAuth2AuthenticationToken[nameAttributeKey: represent the unique id for this user],]
DelegatingFilterProxy  -delegate Regquest To -> FilterChainProxy --assign the req to the appropriate SecurityFilterChain
    securityFilterChain are simply collection of filter that perform security check on the req.
    .. we can define different securityfilterChain to different endpoint with different authentication method.
        ..the only passed requests reach to the ServletRequest.
    for oauth2 2filters to handle the OAUTH2 authorization code grant flow:
    OAutg2AuthorizationRequestRedirectFilter
    OAuth2LoginAuthenticationFilter
        --> /oauth2/authorization/<registrationID>  <-- will intercepted by OAutg2AuthorizationRequestRedirectFilter
        which will generate redirect uri for authorization server.
        - after user agree on consent screen, IDP redirect with auth_code and state_token to protect against CSRF to
        REDIRECT_URL/login/oauth2/code/<registrationID>/?code=&state=
        which delegated to OAuth2LoginAuthenticationFilter then delegate to AuthenticationManager to perform 
            actual authentication --authenticate<Authentication Provider>--> [OidcAuthorizationCodeAuthenticationProvider|
            OAuth2LoginAuthenticationProvider..] --> exchange auth_code with access_token.. 
---
 
   - delegation of authorization, specs mention authentication should happen but not specify how. [OIDC]
  - we can centralize user registration in authorization server instead of
    writing it to each service we have. 
     - adding all what we need in one place like multi-factor authentication...
 - OAuth2 in spring
   - in the past, oauth2 support scattered between a number of projects.
   - 5.2+ spring team decide to unify JWT, JOSE, OAuth2 and OIDC support under spring security.
   - --
   - spring app(client) redirect owner to authorization server  
      for authentication. [spring configure that url for popular authorization server(CommonOAuth2Provider(FB|GOOGLE|OKTA)). if u don't use one of them u need to configure it.()]
       - auth server redirect to redirect-url that only registered from the client.
       - u as a client take that code that sent to the redirect-url in frontChannel and exchange with auth server via tokenUri.
        to debug it [OAuth2AuthorizationCodeAuthenticationProvider]
    - OIDC :
      - provides an identity layer on top of the OAuth 2.0 protocol.
      - Clients can verify the identity of an end-user based on the 
         authentication performed by an authorization server.
      - Gives clients access to basic profile information about the 
         end user via REST.
         redirect url: urDomain/login/oauth2/code/google
         all oidc orovider must use the same path ex:
           [issuer-uri => accounts.google.com]/.well-known/openid-configuration
      id-token: intended to be used for authentication, has a strict standards -> u don't have to worry about changes to the token format.   
---
- ControllerAdvices are designed to assist Controller classes and ExceptionHandlers are for handling exceptions thrown by Controllers.
  - AuthenticationException and AccessDeniedExceptions are usually thrown by AbstractSecurityInterceptor of Spring Security. So, i'm guessing that you won't be able to catch those AuthenticationExceptions using ControllerAdvices, since ExceptionTranslationFilter would already catch them and convert them to appropriate HTTP responses.
  - The better approach is to use exceptionHandling in your WebSecurityConfigurerAdapter. Using that, you can configure a AuthenticationEntryPoint and AccessDeniedHandler. Here, i'm returning a 403 Forbidden for access denied cases and a 401 Unauthorized .
---
https://developer.okta.com/blog/2021/05/05/client-credentials-spring-security
https://github.com/springframeworkguru/spring-6-resttemplate/tree/32-oauth-mock-manager