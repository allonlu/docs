# Initiated-IDP with Artifact

在用户已经登录系统时, SSO可以从IDP端发起, 使得用户在访问SP(Comm100)的资源时可以省去系统生成SAMLRequest跟IDP交换这部分信息, 为保证整个过程的安全性, 我们选择Artifact的方式获取IDP的Assertion.

## 过程

1. 在用户的页面中, 用户知道当前用户已经登录, 可以直接直接让用户请求sp的asc地址 https://livechat.comm100.com/saml2/sso/artifact?SAMLart=artifact_1&target={target_url}
  - artifact_1 由IDP生成, 只能使用一次, 用过以后IDP应该丢失, 避免重放攻击

2. comm100 在接收到artifact, 构造ArtifactResolve, 请求IDP的ArtifactResolutionService地址, 

```xml
<samlp:ArtifactResolve
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"
  xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
  ID="identifier_4"
  Version="2.0"
  IssueInstant="2018-12-05T09:22:04Z"
  Destination="https://idp.example.org/SAML2/ArtifactResolution">
  <saml:Issuer>https://sp.example.com/SAML2</saml:Issuer>
  <!-- an ArtifactResolve message SHOULD be signed -->
  <ds:Signature
    xmlns:ds="http://www.w3.org/2000/09/xmldsig#">...</ds:Signature>
  <samlp:Artifact>''artifact_2''</samlp:Artifact>
</samlp:ArtifactResolve>
```

3. IDP接收到上述请求以后, 会返回ArtifactResponse, 里面包含一个常规的<samlp:Response>, Comm100 可以从这里获取用户的Assertion信息, 从而获取都受信任的授权(IDP)

```xml
 <samlp:ArtifactResponse
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"
    ID="identifier_5"
    InResponseTo="identifier_4"
    Version="2.0"
    IssueInstant="2018-12-05T09:22:05Z">
    <!-- an ArtifactResponse message SHOULD be signed -->
    <ds:Signature
      xmlns:ds="http://www.w3.org/2000/09/xmldsig#">...</ds:Signature>
    <samlp:Status>
      <samlp:StatusCode
        Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>
    </samlp:Status>
    <samlp:Response
      xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"
      xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
      ID="identifier_6"
      InResponseTo="identifier_3"
      Version="2.0"
      IssueInstant="2018-12-05T09:22:05Z"
      Destination="https://sp.example.com/SAML2/SSO/Artifact">
      <saml:Issuer>https://idp.example.org/SAML2</saml:Issuer>
      <ds:Signature
        xmlns:ds="http://www.w3.org/2000/09/xmldsig#">...</ds:Signature>
      <samlp:Status>
        <samlp:StatusCode
          Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>
      </samlp:Status>
      <saml:Assertion
        xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
        ID="identifier_7"
        Version="2.0"
        IssueInstant="2018-12-05T09:22:05Z">
        <saml:Issuer>https://idp.example.org/SAML2</saml:Issuer>
        <!-- a Subject element is required -->
        <saml:Subject>
          <saml:NameID
            Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress">
            user@mail.example.org
          </saml:NameID>
          <saml:SubjectConfirmation
            Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
            <saml:SubjectConfirmationData
              InResponseTo="identifier_3"
              Recipient="https://sp.example.com/SAML2/SSO/Artifact"
              NotOnOrAfter="2018-12-05T09:27:05Z"/>
          </saml:SubjectConfirmation>
        </saml:Subject>
        <saml:Conditions
          NotBefore="2018-12-05T09:17:05Z"
          NotOnOrAfter="2018-12-05T09:27:05Z">
          <saml:AudienceRestriction>
            <saml:Audience>https://sp.example.com/SAML2</saml:Audience>
          </saml:AudienceRestriction>
        </saml:Conditions>
        <saml:AuthnStatement
          AuthnInstant="2018-12-05T09:22:00Z"
          SessionIndex="identifier_7">
          <saml:AuthnContext>
            <saml:AuthnContextClassRef>
              urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
           </saml:AuthnContextClassRef>
          </saml:AuthnContext>
        </saml:AuthnStatement>
      </saml:Assertion>
    </samlp:Response>
  </samlp:ArtifactResponse>
```
4. 重定向到用户需要访问的地址

# Agent Console Extension

支持ticket的extesion, 需要在manifest中增加两个配置项
  - `location`, 可设置的值为`chat_side_bar`, `ticket_side_bar`
    - `chat_side_bar` 显示在Chat窗口的右侧tab中
    - `ticket_side_bar` 显示在Ticket窗口的右侧tab中
  - `page_mode`, 可设置的值为`single`, `multi`, agent console在加载extension时会根据不同的模式加载
    - `single` 针对单页应用, 在所有的chat/ticket中会使用同一个页面, 针对不同的聊天/访客不需要保存不同的状态, 或者单页应用本身维护了各种状态
    - `multi` 针对多页应用, 在每一个打开的chat/ticket中会生成一个实例 (iframe), 切换不同的chat/ticket就会切换显示不同的iframe
      - 针对chat, 在某一个聊天从chat列表中移除时会销毁这个app instance
      - 针对ticket? 什么时候可以销毁?

